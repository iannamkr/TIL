## Background

- Monolithic hell: agile development and deployment becomes impossible...

- Microservice architecture structures an application as a set of loosely coupled services
  - database per service
    - private schema
    - private table
    - loose coupling encapsulated data
  - accelerate software development through parallelization
  - simplify the adoption of new technology stacks
  - ! but no silver bullets

## ACID is not an option
### How to maintain data consistency in microservices ???
```sql
begin transaction
...
select order_total
from orders where customer_id = ?    
... 
select credit_limit
from customers where customer_id = ? 
...
insert into orders...
...
commit transaction
```
- cannot use acid transactions
  - `orders` table is private to order service
  - `customers` table is private to the customer service
 
- 2pc is not an option
  - guarantees consistency
  - ! 2pc coordinator is a SPOF (single point of failure)
  - ! chatty: at least O(4n) messages woth retries O(n^2)
  - ! reduced throughput due to locks
  - ! not supported by many NoSQL databases
  - ! impacts availability (CAP)
   
## Sagas instead of 2pc
   
   
### Create order saga
![image](https://user-images.githubusercontent.com/13671946/71728980-f4be6a00-2e81-11ea-9bd7-5d70622df8bc.png)


### Rollback using compensating transactions
- ACID transactions can simply rollback
- ! developer must write logic to "rollback" eventually consistency transactions
- ! careful design required

### Saga: Every Ti has a Ci
- paper: https://www.cs.cornell.edu/andru/cs711/2002fa/reading/sagas.pdf
- `Saga` concept
  - <img src="https://user-images.githubusercontent.com/13671946/71729371-da38c080-2e82-11ea-859c-68e5ae80fd8d.png" width="400" height="200" />
  - break up a LLTs (long lived transactions) into a set of local transactions or sequence of local transactions for order
  - every Transaction i(Ti) has a compensating transactions (Ci)
    - compensating(i) transactions = undo what transaction(i) did
    - e.g.) T1(success) => T2(failed) => C1(=undo T1)

### Create order sava - `rollback`
![image](https://user-images.githubusercontent.com/13671946/71729981-5e3f7800-2e84-11ea-94e9-caceb7997804.png)

### Sagas complicate api design
- q1. Requests initiates the saga. When to send back the response?
- o1. Send response when saga completes:
  - response specifies the outcome
  - reduced availbility
- o2. Send response immediately after creating the saga (recommended)
  - improved availability
  - ! response does not specify the outcome. Client must poll or be notified
      
### Revised Create Order API
- createOrder()
  - returns id of newly created order
  - not fully validated
- getOrder()
  - called periodically by client to get outcome of validation

### Minimal impact on UI
- UI hides async API from the user
- Saga will usually apper instantaneous (<= 100ms)
 - ! if longer => displays "processing" popup (이건 좀..)
- server can push notification to ui
  
  
### Saga complicate the business logic
- changes are committed by each step of the saga
- other transactions see "inconsistent" data 
  - e.g. Order.state = PENDING (more complex logic)
- interaction between sagas and other operations
  - e.g. what does it mean to cancel a PENDING Order ??
  - "Interrupt" the Create Order Saga
  - Wait for the Create Order saga to complete ??


## References
- SAGAS: https://www.cs.cornell.edu/andru/cs711/2002fa/reading/sagas.pdf
- microservices.io: https://microservices.io/patterns/data/saga.html
- Devoxx: https://www.youtube.com/watch?v=YPbGW3Fnmbc
