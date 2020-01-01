## SQL Syntax, Functions, Sequences, Etc. 

1) `sysdate`
```sql
-- oracle
select sysdate from dual

-- postgresql
select 'now'::datetime
```

2) `decode`

```sql
-- oracle
SELECT product_id, 
       decode (warehouse_id, 
               1, ’southlake’, 
               2, ’san francisco’, 
               3, ’new jersey’, 
               4, ’seattle’, 
               ’non-domestic’) quantity_on_hand 
FROM   inventories;

-- postgresql
SELECT a, 
       CASE 
         WHEN a = 1 THEN 'one' 
         WHEN a = 2 THEN 'two' 
         ELSE 'other' 
       END 
FROM   test; 
```

3) `nvl`
```sql
-- oracle
SELECT employeeid, 
       Nvl(hire_date, SYSDATE) 
FROM   employee 
WHERE  employeeid = 10; 

-- postgresql
SELECT employeeid, 
       Coalesce(hire_date, 'now' :: datetime) 
FROM   employee 
WHERE  employeeid = 10; 
```

4) `outer join` 
- oracle: (+)
- postgresql: not supported, use a union

5) `minus`
```sql 
-- oracle
SELECT product_id 
FROM   inventories 
MINUS 
SELECT product_id 
FROM   order_items; 

-- postgresql
SELECT product_id 
FROM   inventories 
EXCEPT 
SELECT product_id 
FROM   order_items; 
```

6) `unique & distinct` 
```sql
-- oracle (==distinct)
select unique col1, col2 from table1;

-- postgresql (not allow 'select unique')
select distinct col1, col2 from table1;
```

7) `mod`
```sql
--oracle
select mod(10,4) from dual;

-- postgresql
select 10 % 4; 
```

8) `rownum`
```sql
--oracle
select * from employees where rownum < 10 order by name;
select rownum, name from employees order by name;

-- postgresql
select * from employees order by name limit 10; 
select oid, name from employees order by name; 
```

### ROWNUM
```sql
ROWNUM = N
LIMIT 1 OFFSET N

ROWNUM < or <=N
LIMIT N

ROWNUM > or >= N
LIMIT ALL OFFSET N
```

### Empty String & NULL
```sql
field1 IS NULL
coalesce(field1::text, '') = ''

field2 IS NOT NULL
field2 IS NOT NULL AND field2::text <> ''
```

### JOIN
```sql
-- LEFT OUTER JOIN
SELECT * FROM a, b WHERE a.id = b.id (+)
SELECT * FROM a LEFT OUTER JOIN b ON (id)

-- RIGHT OUTER JOIN
SELECT * FROM a, b, c WHERE a.id = b.id (+) AND a.id (+) = c.id
SELECT * FROM a LEFT OUTER JOIN b ON (a. id = b.id) RIGHT OUTER JOIN c ON (a.id = c.id)

-- FULL OUTER JOIN
SELECT * FROM a, b WHERE a.id = b.id (+) UNION ALL SELECT * FROM a, b WHERE a.id (+) = b.id AND a.id = NULL
SELECT * FROM a FULL OUTER JOIN b ON (a.id = b.id)
```

### DECODE
```sql
DECODE (expression, search, result [, search, result]... [, default])
CASE WHEN expr = search THEN result ... ELSE default END
```
