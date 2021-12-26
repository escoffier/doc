# PostgresSQL

## Test

```bash
/usr/local/pgsql/bin/psql testdb

select datname, oid from pg_database;
```

```sql
select datname, oid from pg_database;

select relname, oid, relfilenode from pg_class where relname = 'accounts';
```

## TRANSACTION

### practice

#### console1

```bash
postgres=# begin;
BEGIN
postgres=# update accounts set password='4444444' where user_id=3;
UPDATE 1
postgres=# select * from accounts;
 user_id | username | password 
---------+----------+----------
       2 | tom      | 1234
       1 | ribbin   | 123
       4 | 44       | 55
       5 | 55       | 66
       3 | 33       | 4444444
(5 rows)
 
postgres=# commit;
COMMIT
```

### console2

```bash
postgres=# BEGIN TRANSACTION ISOLATION LEVEl REPEATABLE READ;
BEGIN
postgres=# select * from accounts where user_id=3;
 user_id | username | password 
---------+----------+----------
       3 | 33       | 334444
(1 row)

postgres=# select * from accounts where user_id=3;
 user_id | username | password 
---------+----------+----------
       3 | 33       | 334444
(1 row)

postgres=# update accounts set password='9994444' where user_id=3;
ERROR:  could not serialize access due to concurrent update
postgres=# commit ;
ROLLBACK
```
