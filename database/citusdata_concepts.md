## Nodes
### Coordinator and Workers
PostgreSQL 의 extension 으로써 `shared nothing` architecture 기반으로 scale-out이 용이하며, 단일 computer에서 처리하는 것보다 많은 CPU 코어와 데이터를 저장할 수 있다.

모든 클러스터는 하나의 특별한 node인 `coordinator`를 가지고 있으며, Application은 query를 `coordinator` 노드에게 전송하면 관련된 `worker`에게 전달하여 결과를 누적한다. 

`coordinator`는 테이블에 대한 메타 정보를 가지고 있기 때문에, 각 query에 대하여 `coordinator`는 단일 `worker` 노드에 라우팅하거나, 필요한 데이터가 있는 한 개 이상의 노드에 병렬로 처리한다. 

## Distributed Data
### Table Types
Citus cluster는 목적에 따라 3 가지의 테이블 타입이 존재한다.

#### 1. Distributed Tables
; _normal table로부터 horizontally partitioning 된 부분 테이블_

![image](https://user-images.githubusercontent.com/13671946/77988640-d15b6c00-7357-11ea-9648-49ca23a6c538.png)

`table`은 `table_1001`, `table_1002` 등으로 `worker`에 저장되고, 이를 `shard`라고 한다.

Citus는 SQL 뿐만 아니라 DDL도 수행되며, 분산 테이블의 schema를 변경하는 경우 모든 worker의 shard에 대해 update가 수행된다.

#### Distribution Column 
; _sharding을 위해 선택된 column_

Citus는 특정 테이블의 `distribution column`을 기준으로 `table`을 shard로 쪼갠다. 적절한 `distribution column`을 선택하는 것이 성능에 매우 중요하다. (http://docs.citusdata.com/en/v9.2/sharding/data_modeling.html#distributed-data-modeling
)

#### 2. Reference Table
; _단일 shard로 모든 worker에 복제된 테이블_

distributed table의 일종으로 하나의 shard에 모든 데이터가 저장되어 모든 worker에 복제된다. 따라서 해당 테이블은 어떤 `worker`에서도 네트워크 비용 없이 로컬에서 수행된다. 

reference table은 shard를 구별할 필요가 없으므로 별도의 distribution column이 없다.

일반적으로 reference table은 enum 같이 매우 작은 데이터를 저장하는 것에 용이하다. reference table를 처리할때 2pc로 수행되므로 데이터에 대한 write, update, delete에 대한 consistency는 보장된다.

#### 3. Local Table
;_sharding 되지 않은 기본 테이블_

coordinator 노드는 Citus extension이 설치된 PostgreSQL 데이터베이스이다. 즉 기본적으로 coordinator를 통해 테이블을 만들고 샤딩을 하지 않을 수 있다. 이를 local table 이라 한다.  

관리자 테이블이나 로그인, 인증을 위한 사용자 테이블과 같이 join을 필요로 하지 않은 테이블에 적합하다.

### Shards
; _horizontally partitioned table_

`coordinator`에는 모든 distributed table에 대해서 shard 별 메타데이터를 저장하고 있는 `pg_dist_shard` 테이블을 가지고 있다. 

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

coordinator는 어느 shard 에 `github_events`가 있는 지 알고자 하는 경우, 해당 row의 distribution column에 해당하는 hash 값이 
어느 범위에 존재하는 지를 판단한다.


### ShardPlacements
특정 shard가 어느 worker에 존재하는지를 확인하기 위해서는 `pg_dist_placement` 메타테이블을 확인하면 된다.
```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;

┌─────────┬───────────┬──────────┐
│ shardid │ nodename  │ nodeport │
├─────────┼───────────┼──────────┤
│  102027 │ localhost │     5433 │
└─────────┴───────────┴──────────┘
```

> Coordinator Metadata: http://docs.citusdata.com/en/v9.2/develop/api_metadata.html#metadata-tables

### Co-Location


### Parallelism
