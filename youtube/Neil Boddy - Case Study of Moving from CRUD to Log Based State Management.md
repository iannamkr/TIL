## Case Study of Moving From CRUD to Log Based State Management

### Context: Manufacturing Master Data

![image](https://user-images.githubusercontent.com/13671946/71622127-11a33500-2c17-11ea-90e9-7698ef21c29b.png)

#### 1. typical pipe processing of event-driven architecture
- end to end batch
- 24/7 stream 

#### 2. each stage is/has...
- connected/interconnected with queues
- single responsibility component
- one thread / concurrency for performance
- fire-and-forget

#### 3. each stage do/work...
- read files/databases/rest calls
- decrypt/unzip/compaction(when highly fragmented)
- canonicalization (distinct, ordering, remove repeated calcuiations) [*](https://en.wikipedia.org/wiki/Canonicalization)
- derivation / validation [*](https://docs.oracle.com/cd/E22982_01/doc.462/e18820/proc.htm#CHDCGHHD)


### Motivations for Rethinking Data Management
![image](https://user-images.githubusercontent.com/13671946/71615396-7dc07180-2bf4-11ea-956d-805fca7be617.png)

#### 1. typical relational incremental crud
- processing
  - check equity of data
  - fixed forms of data
  - de-indentification of data, legal

- characteristics
  - very strict sequential order
  - one to many relationships 
    - thousands of attributes from hundreds of flows
  - multi-structured data
    - have different schema
  - very structurally complex
    - expensive variation when need custom flows
    - need experts for variation
  - hard to correct state and unwind
    - not fault-tolerant to data
    - not fault-tolerant to system failure
    - not ability recover quickly
 
### Fragility of Incremental CRUD Batch Processing

![image](https://user-images.githubusercontent.com/13671946/71623196-21724780-2c1e-11ea-89f4-096a3337b473.png)

#### 1. typical crud processing of stream data coming in d1- dn
- `lookup` 
- `merge`: figure out whar are the new/update/remove/not updated from complex structure/relationship
- `save`

#### 2. example
|   | Bch1 | Bch2   | Bch3   | What if Bch1 -> Bch3 -> Bch2 |
|---|------|--------|--------|----------------------|
| A | NEW  |        |        | OK                     | 
| B | NEW  | UPDATE | UPDATE | stale                |
| C | NEW  | REMOVE |        |                      |
| D | NEW  | REMOVE | NEW | missing              |
| E |      | NEW    | REMOVE | phantom              |
| F |      | NEW    |        | degenerate           |
| G |      |        | NEW    | rejected RI          |

- description
  - `B`(stale): Bch3값이 아닌 Bch2값을 갖음
  - `D`(missing): 새로 추가되었어야하나 누락되버림 
  - `E`(phantom): 삭제되었어야하나 존재하는 원치 않는 값 (not expected)
  - `F`(degenerate): `G`가 누락되어 degenerate 됨 (missing relationship)
  - `G`(rejected RI): `G`는 `F`의 child component이나 참조무결성(referential integrity)에 의해 거부됨

### Rethinking the Batch DataSet
![image](https://user-images.githubusercontent.com/13671946/71640896-1b3fa200-2cd6-11ea-9e6d-49ab99c21fde.png)

#### 1. crud to log-based system 
- delta batch
  - dependency on the full universe
  - contain all of the change records
  - contain only bits of the information that have changed
  - do not have any meaningful information 
  - need merge with state have accumulated from the full snapshot

### Composition of Partial State as an Event Stream
![image](https://user-images.githubusercontent.com/13671946/71640929-d6683b00-2cd6-11ea-9bd8-12a87328093a.png)

#### 1. what is aggregate ???
  - _“An aggregate is a cluster of associated objects that we treat as a unit for the purpose of data changes. Each aggregate has a root and a boundary.” - Eric Evan_
  - Unit if logical consistency
  - Unit of work
  - Unit of atomic storage consistency
  - Intuitive data structure

#### 2. aggregate compisition at T1, T2, T3
- T1: head of queue, initial data
- T2: captures the update engine and cabin
- T3: captures the final update engine


## Reference
- https://microservices.io/index.html
- https://www.youtube.com/watch?v=uNodMFBgsDs
