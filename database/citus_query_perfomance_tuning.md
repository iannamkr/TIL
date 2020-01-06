### Table Distribution and Shards
- choosing the right distribution column
  - helps Citus push down several operations directly to the worker shards and prune away unrelated shards
  - `filter`: Citus uses the distribution column ranges to prune away unrelated shards, ensuring that the query hits only those shards which overlap with the WHERE clause ranges
  - `joins`: Citus executes the join only between those shards which have matching / overlapping distribution column ranges

- Choose the right distribution column
- Citus can push down several operations directly to the worker shards if they are based on the distribution column

### PostgreSQL tuning
- coordinator: Partitions query into fragment queries, and sends them to the workers for parallel processing
- workers: Apply PostgreSQL's standard planning and execution logic for the fragmented queries.

#### Example
  - Run the `EXPLAIN` command to provide distributed query execution
  ```sql
  EXPLAIN
   SELECT date_trunc('minute', created_at) AS minute,
          sum((payload->>'distinct_size')::int) AS num_commits
     FROM github_events
    WHERE event_type = 'PushEvent'
    GROUP BY minute
    ORDER BY minute;
  ```
  ```sql
  Sort  (cost=0.00..0.00 rows=0 width=0)
    Sort Key: remote_scan.minute
    ->  HashAggregate  (cost=0.00..0.00 rows=0 width=0)
      Group Key: remote_scan.minute
      ->  Custom Scan (Citus Adaptive)  (cost=0.00..0.00 rows=0 width=0)
        Task Count: 32
        Tasks Shown: One of 32
        ->  Task
          Node: host=localhost port=5433 dbname=postgres
          ->  HashAggregate  (cost=93.42..98.36 rows=395 width=16)
            Group Key: date_trunc('minute'::text, created_at)
            ->  Seq Scan on github_events_102042 github_events  (cost=0.00..88.20 rows=418 width=503)
              Filter: (event_type = 'PushEvent'::text)
  (13 rows)
  ```
  - 1. Chose the Citus adaptive executor to execute this query
  ```
  ->  Custom Scan (Citus Real-Time)  (cost=0.00..0.00 rows=0 width=0)
  Task Count: 32
  Tasks Shown: One of 32
  ->  Task
    Node: host=localhost port=5433 dbname=postgres
  ```
  - 2. tune query performance for the shard using standard PostgreSQL techniques
  ```
  ->  HashAggregate  (cost=93.42..98.36 rows=395 width=16)
  Group Key: date_trunc('minute'::text, created_at)
  ->  Seq Scan on github_events_102042 github_events  (cost=0.00..88.20 rows=418 width=503)
    Filter: (event_type = 'PushEvent'::text)
  ```
  - 3. Verify that workers are all behaving perperly
  ```
  SET citus.explain_all_tasks = 1;
  EXPLAIN
   SELECT date_trunc('minute', created_at) AS minute,
          sum((payload->>'distinct_size')::int) AS num_commits
     FROM github_events
    WHERE event_type = 'PushEvent'
    GROUP BY minute
    ORDER BY minute;
  ```
  ```
   Sort  (cost=0.00..0.00 rows=0 width=0)
     Sort Key: remote_scan.minute
     ->  HashAggregate  (cost=0.00..0.00 rows=0 width=0)
       Group Key: remote_scan.minute
       ->  Custom Scan (Citus Adaptive)  (cost=0.00..0.00 rows=0 width=0)
         Task Count: 32
         Tasks Shown: All
         ->  Task
           Node: host=localhost port=5433 dbname=postgres
           ->  HashAggregate  (cost=93.42..98.36 rows=395 width=16)
             Group Key: date_trunc('minute'::text, created_at)
             ->  Seq Scan on github_events_102042 github_events  (cost=0.00..88.20 rows=418 width=503)
               Filter: (event_type = 'PushEvent'::text)
         ->  Task
           Node: host=localhost port=5434 dbname=postgres
           ->  HashAggregate  (cost=103.21..108.57 rows=429 width=16)
             Group Key: date_trunc('minute'::text, created_at)
             ->  Seq Scan on github_events_102043 github_events  (cost=0.00..97.47 rows=459 width=492)
               Filter: (event_type = 'PushEvent'::text)
         --
         -- ... repeats for all 32 tasks
         --     alternating between workers one and two
         --     (running in this case locally on ports 5433, 5434)
         --

  (199 rows)
  ```
  - Differences in worker execution can be caused by tuning configuration differences
    - uneven data distribution across shards
    - hardware differences

### Distributed Query Performance Tuning
- ***we recommend that you measure query times on your distributed cluster and compare them with the single shard performance.***
  - 1. running the query on the coordinator node
  - 2. running one of the fragment queries on the worker nodes
  - 3. find bottleneck
    - amount of time spent on the worker nodes
    - amount of time spent in fetching data to the coordinator node
  
### General
#### factors which impacts performance
- `INSERT`

  | Coordinator Node    | Worker Nodes            | Latency (ms) | Transactions per sec |
  |---------------------|-------------------------|--------------|----------------------|
  | 2 cores - 7.5GB RAM | 2 * (1 core - 15GB RAM) | 28.5         | 9,000                |
  | 4 cores - 15GB RAM  | 2 * (1 core - 15GB RAM) | 15.3         | 16,600               |
  | 8 cores - 30GB RAM  | 2 * (1 core - 15GB RAM) | 15.2         | 16,700               |
  | 8 cores - 30GB RAM  | 4 * (1 core - 15GB RAM) | 8.6          | 29,600               |

- `SELECT`: executor can be selected by setting the `citus.task_executor_type`
  - real-time executor
    - simple key-value lookups / sub-second respond to aggregation and joins
  - task tracker executor
    - repartitioning and shuffling of data across the workers
- `citus.limit_clause_row_fetch_count`: set the number of rows to fetch from each task while calculating. [#Limit Pushdown](https://docs.citusdata.com/en/v9.0/develop/reference_sql.html#limit-pushdown)
- `citus.count_distinct_error_rate`: count distinct approximations [#Count (Distinct) Aggregates](https://docs.citusdata.com/en/v9.0/develop/reference_sql.html#count-distinct)

#### Subquery/CTE Network Overhead
- **forced to execute subqueries before executing the main query, copying the intermediate subquery results to other worker nodes for use by the main query**
- subqueries are executed in a seperated step, avoiding sending too much data between worker nodes.
```
ERROR:  the intermediate result size exceeds citus.max_intermediate_result_size (currently 512 kB)
DETAIL:  Citus restricts the size of intermediate results of complex subqueries and CTEs to avoid accidentally pulling large result sets into once place.
HINT:  To run the current query, set citus.max_intermediate_result_size to a higher value or -1 to disable.
```
- avoiding this happens, use limits of filters inside CTEs/subqueries
```
-- It's slow to retrieve all rows and limit afterward

WITH cte_slow AS (SELECT * FROM users_table)
SELECT * FROM cte_slow LIMIT 10;

-- Limiting inside makes the intermediate results small

WITH cte_fast AS (SELECT * FROM users_table LIMIT 10)
SELECT * FROM cte_fast;
```
