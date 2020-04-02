## Table-level Locks
SQL명령어에 따른 테이블 레벨에서의 Lock 획득 조건

- ACCESS SHARE
  - `SELECT`
  
- ROW SHARE
  - `SELECT FOR UPDATE`
  - `SELECT FOR SHARE`

- ROW EXCLUSIVE
  - `INSERT`
  - `UPDATE`
  - `DELETE`
  
- SHARE UPDATE EXCLUSIVE
  - `VACUUM`
  - `ANALZE`
  - `CREATE INDEX CONCURRENTLY`
  - `ALTER TABLE`
  
- SHARE
  - `CREATE INDEX`

- SHARE ROW EXCLUSIVE
  - Not acquired implicitly by any command.

- EXCLUSIVE
  - Not acquired implicitly by any command.
  
- ACCESS EXCLUSIVE
  - `ALTER TABLE` 
  - `DROP TABLE` 
  - `TRUNCATE`
  - `REINDEX`
  - `CLUSTER`
  - `VACUUM FULL`
  
  
## Lock conflicts
<img width="720" alt="스크린샷 2020-04-02 오후 6 48 27" src="https://user-images.githubusercontent.com/13671946/78235511-b2093e00-7513-11ea-9797-8462a166cfc0.png">


## Row-level Locks

### V 9.1, 9.2
- SHARE
  - `SELECT FOR UPDATE`
  
- EXCLUSIVE
  - `UPDATE`
  - `DELETE`
  
  
### V 9.3, 9.4
- FOR UPDATE
  - `UPDATE`
  - `DELETE`
  - `SELECT FOR UPDATE`
  - `SELECT FOR NO KEY UPDATE`
  - `SELECT FOR SHARE`
  - `SELECT FOR KEY SHARE`
  - `DELETE`
  - `UPDATE`
  
- FOR NO KEY UPDATE
  - `UPDATE`
  
- FOR SHARE
  - `UPDATE`
  - `DELETE`
  - `SELECT FOR UPDATE`
  - `SELECT FOR NO KEY UPDATE`
  
- FOR KEY SHARE
  - `SELECT FOR UPDATE`
  - `DELETE`
  - `UPDATE`
  

