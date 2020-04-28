## Kudu: Storage for Fast Analytics on Fast Data

1. storage engine for `structured` data
2. supports low-latency `random access`
3. horizontal partitioning and replicates using Raft 


### Introduction
- scalable althernative
  - structured
    - BAD: update records, random access 
    - e.g: HDFS, AVRO, Parquet
  - semi-structured
    - GOOD: record-level reads & writes 
    - BAD: sequential read such as SQL-based analytics & ML 
    - e.g: HBase, Cassandra
 - structured + semi-structured ? 
   - e.g: HBase => streaming ingest & update, Parquet => batch analysis
   - complex architecture, code, patterns and synchronization of data between the two systems
   - manage consistent backups, security policies, monitoring multiple systems
   - lag between new data (HBase)
   - expensive rewriting and swapping of partitions and manual intervention (immutablility)

**! Kudu is a new storage to fill this gap between sequential-access storage and random-access systems**

### Kudu at a high level
#### Tables and schemas
- storage system for tables of structured data
- well-defined schema, finite number of columns
  - name, type, nullable
- primary key, define the schema at the time of creation
- type specific columnar encodings
- expose sql-like metadata

### Write operations
- must fully specify a primary key for Insert, Update, Delete APIs
- offers APIs in Java, C++, Python
- not offer any multi-row transactional APIs, executes as its own transaction
  - modifications within a single row are always atomically across columns

### Read operations
- offers only a Scan operations 
- offers only two predicates to filter
  - comparison (column, constant)
  - composite (primary key ranges)

### Other APIs
- APIs for Spark, MapReduce, Impala
> Hadoop ecosystem gains much of its performance by scheduling for data locality

### Consistency Model
- snapshot consistency (default)
  - guaranteed to yield a snapshot with no anomalies 
- not provide a external consistency (= strong consistency)
  - not guaranteed with other components in different settings
  ![image](https://user-images.githubusercontent.com/13671946/80487995-a8e78180-8998-11ea-862a-e1fb7b867df9.png)

  - offers option to manually propagate timestamps between clients
    - preserving causal relationship
    - commit-wait for too complex timestamp tokens
