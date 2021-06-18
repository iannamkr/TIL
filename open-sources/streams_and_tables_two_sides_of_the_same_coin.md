### streams and tables: two sides of the same coin

- data stream 처리에서 논리적, 물리적 순서성을 보장하기 위한 `Dual stream Model`을 제안.
- `Dual stream Model`은 별도의 buffering, ordering 없이 논리적, 물리적 순서성의 불일치 문제를 해결.
- `Dual stream Model`이 갖는 correctness, latency, processing cost 에 대한 trade-off 를 논의.


> 귀찮으니 영어로 ...

#### Data Streams
; implicitly defines physical storage and processing model 
![image](https://user-images.githubusercontent.com/13671946/122349480-429c3480-cf87-11eb-96c2-c14b4a98b25e.png)

- `offset`: a record is its unique sequence number, as asigned when appended to a data stream.
- `timestamp`: assigned by its source. multiple records can have the same timestamp. yet th number of records with the same timestamp is finite.
- `payload`: to capture the payload of a record, we adopt a model based key-value pairs.


- `total order` is given by the offsets.
- `logical order` is induced by the timestamps of the records.
  - offset is a `tie breaker` to derive a logical order that is strict and total over all records.

![image](https://user-images.githubusercontent.com/13671946/122351023-b3901c00-cf88-11eb-9e40-8300ce641596.png)
`if r1 < r2 then r1.timestamp < r2.timestamp or (r1.timestamp = r2.timestamp and r1.offset < r2.offset)`

- `physical order` may be inconsistent with their logical order, results from imperfect clock synchronization, network delays, data buffering, in particular in distributed application.

#### Streaming Operators
- data processing is performed as a __single linear scan__ over the input data streams. records are processed in physical order.
- stateless operators (e.g. `filter`, `projection`, `map`, `flatmap`)
- stateful operators (e.g. `agg`, `join`)
