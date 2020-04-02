## Perf tuning is an iterative process

- Ensure stability of the job
- Solve performance issue
- Adjust speed-cost trade-off

### Rules of thumb
- number of partitions: 3 * number of cores in the cluster
- number of cores per executor: 4-8
- memory per executor: 85% * (node memory / executors by node)
  - reservce yarn, jvm memory


### Common issues: lack of parallelism

#### Configuation
- 26 instances n1-highmem-16
- spark.executor.cores = 16
<img width="142" alt="스크린샷 2020-04-02 오후 1 59 41" src="https://user-images.githubusercontent.com/13671946/78212399-8d4ca080-74ea-11ea-9c50-d6683650fda0.png">

`spark.executor.cores` 값을 16으로 했는데, 8개의 core만 사용되고 있는 경우

#### The reason
- spark.sql.shuffle.partitions 값을 설정하지 않아 기본 값인 200으로 수행됨
- number of tasks per executor = number of partitions / number of cores per executor = 200 / 26 = 7.7
- core 갯수의 약 48%% 정도만 사용하고 있으므로 병렬성이 떨어짐


#### Fix
1. spark.sql.shuffle.partitions = 400+
2. spark.sql.adaptive.enabled=true (SPARK-23128)


### Common issues: shuffle spill
![image](https://user-images.githubusercontent.com/13671946/78213289-398f8680-74ed-11ea-9049-bb3efba21faf.png)

map stage 단계에서 deserialized 된 데이터의 크기가 가용 메모리를 초과한 경우, disk에 임시로 출력(spill)하게 됨



### Fix
- partition의 갯수를 늘여서 task 당 처리해야하는 input data의 사이즈를 축소한다.
- task 당 가용 메모리를 늘린다.
  - by `increasing` spark executor memory
  - by `decreasing` the number of cores per executor


### Common issues: data skew
<img width="425" alt="스크린샷 2020-04-02 오후 2 21 53" src="https://user-images.githubusercontent.com/13671946/78213509-d6522400-74ed-11ea-998e-eca2994a2bf8.png">

skewness으로 인하여 일부 task의 처리하는 시간이 다른 task에 비해 비정상적으로 오래 걸리는 경우 (straggler)

### Fix
- partition key를 변경한다. 
- broadcast join 을 사용한다.
- salted key를 사용한다. 








### Ref
- https://databricks.com/session_eu19/how-to-automate-performance-tuning-for-apache-spark
