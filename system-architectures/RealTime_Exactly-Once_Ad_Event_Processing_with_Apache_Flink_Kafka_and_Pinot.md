__ref: https://eng.uber.com/real-time-exactly-once-ad-event-processing/__



#### 1. Exactly-Once
- `Flink` uses a `KafkaConsumer` with `read_commited` mode `enabled`
- generate random unique identifiers `UUID`, idempotency key for every record produced by the `Aggregation job`
- using `two-phase commit protocol` ensures kafka read-offsets stored in the checkpoint are always in line with the commited messages

#### 2. Upsert in `Pinot`
- update existing data with the changelog in `Kafka`, and deliver an accurate view in the real-time analytics results
- https://docs.pinot.apache.org/basics/data-import/upsert

#### 3. Aggregation Job
![image](https://user-images.githubusercontent.com/13671946/160058733-806b2761-100f-4368-ba3f-a2f3fd59fdd7.png)

1. Data Cleansing
- `Filter`: validation event from `Kafka`
- `Keyby`: partition data into logical groups
- `Map`: deduplicate event from `Kafka`
2. Persistence for Order Attribution
3. Aggregation
- key; id + time bucket 
- put the events into one minute length `Tumbling window`
  - acceptable for dependent clients who rely on the aggregated data 
  - small enough granularity for analysis(-> rolled up to one hour, 6 hour ...)
4. Aggregation function ; count all the clicks and impressions with the `Tumbling window`

#### 4. Record UUID Generation
- generate a record `UUID`, used as an idempotency and deduplication key
- once a message is inserted to `Kafka` and commited, then the record `UUID` can be used for deduplication in `Hive`


#### 5. Attribution Job
![image](https://user-images.githubusercontent.com/13671946/160058778-d4bd9273-f495-467e-b203-fc7736ec285d.png)


#### 6. Union and Laod Job
![image](https://user-images.githubusercontent.com/13671946/160058803-80bd1d68-3e9e-4300-a196-1bc599b05478.png)
- for maintain `exactly-once semantics` we leverage the upsert feature in `Pinot`

> Patterns for exactly-once delivery using kafka and cdc
> 1. transactional outbox pattern
> 2. upsert-deduping pattern
> https://www.linkedin.com/pulse/event-driven-data-architecture-patterns-exactly-once-delivery-singh


![image](https://user-images.githubusercontent.com/13671946/160058733-806b2761-100f-4368-ba3f-a2f3fd59fdd7.png)

---

#### 1. Speed
- main bottleneck is `Flink` checkpoint interval
  - have to wait for the checkpoint to be committed, default is 10minutes
  - not real-time but reasonable time period for customers

#### 2. Reliability
- cross-region replication
> ![image](https://user-images.githubusercontent.com/13671946/160061074-9c7a4daf-b19e-44ee-a8b5-bf8ab9d7c08c.png)

- `Flink`'s checkpointing 
- main pain point comes from `Aggreation job` itself

#### 3. Accuracy
- mix of `exactly-once` semantics and idempotency on `Kafka/Flink` and upsert on `Pinot` provide accuracy gurantees.
