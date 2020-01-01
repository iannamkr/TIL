#### Transactional Outbox
![image](https://user-images.githubusercontent.com/13671946/71627444-c6e4e580-2c35-11ea-9e4f-8368c413a364.png)
- A service uses relational database inserts messages/events, `OUTBOX` table
- Message relay process publishes the messages/events from `OUTBOX` table  to message broker
 - issues...
   - `eventual consistency`: `ORDER` table 과 동일한 transaction으로 처리되므로 message/event 에 따라 eventual consistency가 보장됨
   - `idempotent` receiver needed: `MessageRelay` 가 동일한 message/event를 여러 번 발행할 수 있으므로  message consumer는 처리된 message/event ID를 tracking 해서 idempotent 를 만족시켜야한다.
     - e.g) crash after publishing message/event but before recording the fact that it has done so (message/event 발행 후 이를 기록하기 전 crash가 발생한 경우)
