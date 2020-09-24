on studying...

https://pulsar.apache.org/docs/en/cookbooks-tiered-storage/

### Tiered Storage
- Tiered Storage 는 BookKeeper 공간을 별도 storage(amazon s3, google cloud storage, hadoop) 으로 offload하여 저장비용을 절약함
- 특정 토픽에 있는 데이터를 장기간 보관해야하는 경우 사용한다.

### Offloading Mechanism
- Pulsar의 토픽은 정렬된 segment 목록으로 구성된 managed ledger에 기록된다.
- Tired Storage Offloading mechanism은 세그먼트 기반의 아키텍쳐에 용이하다.
