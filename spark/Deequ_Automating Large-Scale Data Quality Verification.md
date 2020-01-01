## Automating Large-Scale Data Quality Verification
**Our vision is that users should be able to write ‘unit-tests’ for data**

### Background
> 1. Missing or incorrect information seriously compromises any decision process
> downstream
> 2. Tedious task for everyone involved in data processing is to verify the quality of
> their data

- Enables **‘unit tests’ for data**
  - execute the resulting constraint validation workload by translating it to aggregation queries on
Apache Spark
- Supports the incremental validation of data quality on growing datasets and and leverages
machine learning
  - `predictability` of a column
  - detecting anomalies in historic data quality time series


### Introduction
- The quality of data has effects across teams and organizational boundaries, especially in
large organizations with complex systems that result in complex data dependencies
  - machine learning (ML) techniques are often `highly sensitive` on input data
  - `changes in data` can be very hard to detect
  - many data sources do not support `integrity contraints` and data quality checks
  - there is not even an accompanying schema available, as the data is consumed in a schema-on-read manner

- **pressing need for increased automation of data validation**
  - `declarativity` ‘how’ their data should look like
  - `flexibility` able to leverage external data and custom code for validation
  - `continuousity` supports the incremental computation of quality metrics on growing datasets
  - `scalability` data validation systems should seamlessly scale to large datasets
  
### Implementation

#### Table1. Constraints available for composing user-defined data quality checks

![image](https://user-images.githubusercontent.com/13671946/66255674-763e2780-e7c1-11e9-9f35-bf3644dd9653.png)

#### Table2. Computable metrics to base constraints on

![image](https://user-images.githubusercontent.com/13671946/66255690-a08fe500-e7c1-11e9-8424-91ad388d3a1d.png)

####  Figure 2: System architecture
: users declaratively define checks and constraints to combine with their
verification code. The system identifies the required
metrics and efficiently computes them in the runtime layer. The history of metrics and intermediate
states of incremental computations are maintained
in AWS storage services.

![image](https://user-images.githubusercontent.com/13671946/66255699-bdc4b380-e7c1-11e9-8fee-85de783a3b18.png)

### Example
- dataset: https://www.kaggle.com/dgomonov/new-york-city-airbnb-open-data

```scala
"wrangling and cleaning" in
  withSparkSession { spark =>
    val initial = DataProvider.loadFromResources(spark, path)
    val verificationResult = VerificationSuite()
      .onData(initial)
      .addCheck(
        Check(CheckLevel.Error, "unit testing data")
          .isComplete("id")
          .isComplete("name")
          .isComplete("host_id")
          .isUnique("id")
          .isUnique("host_id")
      )
      .run()

    interpretVerificationResult(verificationResult)
  }

  def interpretVerificationResult(verificationResult: VerificationResult): Unit = {
    if(verificationResult.status != CheckStatus.Success) {
      val resultsForAllConstraints = verificationResult.checkResults
        .flatMap { case (_, checkResult) => checkResult.constraintResults }
      resultsForAllConstraints
        .filter {
          _.status != ConstraintStatus.Success
        }
        .foreach { result => println(s"${result.constraint}: ${result.message.get}") }
    }
  }
```

```bash
CompletenessConstraint(Completeness(name,None)): Value: 0.9993479899753459 does not meet the constraint requirement!
CompletenessConstraint(Completeness(host_id,None)): Value: 0.9962305670449683 does not meet the constraint requirement!
UniquenessConstraint(Uniqueness(List(id))): Value: 0.9995721184213208 does not meet the constraint requirement!
UniquenessConstraint(Uniqueness(List(host_id))): Value: 0.6589172558528087 does not meet the constraint requirement!
```
