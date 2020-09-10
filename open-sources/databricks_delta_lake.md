# Delta Lake Architecture
- Full ACID Transaction
  - atoic, sonstistent, isolated, durable
- Versioned parquet files
  - delta transaction log keep track of all operations
- Efficient Upserts
  - merge, delete, update
- Small file compaction
  - optimize and vacuum
- Z-Order partitioning w/ up to 100x perf
  - new multidimensional partitioning enables data skipping
- Time travel
  - audit history, pipeline debugging, data reproducibility
  
![image](https://user-images.githubusercontent.com/13671946/92706229-4dd68a00-f38f-11ea-96dd-1be4b844c38e.png)


- Bronze: 원천 데이터
- Silver: 1차적으로 정제된 데이터
- Gold: 비즈니스 로직이 적용된 데이터

` <Bronze> => (data prep/etl) => <Silver> => (feature extraction/agg) => <Gold> => (data rollups) => serving `



#### ref
- https://docs.microsoft.com/en-us/learn/modules/describe-azure-databricks-delta-lake-architecture
- https://www.youtube.com/watch?v=s88paHD5sVw

.
.
.
.
.

(on writting...)
