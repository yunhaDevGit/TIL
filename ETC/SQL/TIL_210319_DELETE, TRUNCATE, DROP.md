## DELETE, TRUNCATE, DROP

```sql
DELETE FROM dbtable;
TRUNCATE TABLE dbtable;
DROP TABLE dbtable;
```

- 테이블을 날릴 때 사용하는 명령어



#### DELETE

```sql
DELETE FROM dbtable;
DELETE FROM dbtable WHERE 조건;
ROLLBACK;
COMMIT;
```

- where 절 사용 가능
- 테이블에 있는 데이터를 하나 하나 선택하여 지운다.
- where 절을 쓰지 않고 테이블의 모든 데이터를 삭제하도록 하더라도, **내부적으로는 한줄 한줄 일일이 제거하는 과정**을 거친다
  - 쿼리문 처리 속도가 **느리다**
  - 원하는 **데이터만 골라서 삭제**할 때는 **DELETE**가 맞지만, **테이블 전체 데이터를 삭제**하고자 할 때는 **TRUNCATE**를 사용한다.
- 데이터를 삭제하더라도 데이터가 담겨있던 Storage는 Release가 되지 않아, DELETE 된 데이터는 COMMIT 명령어를 사용하기 전이라면, ROLLBACK 명령어를 통해 되돌릴 수 있다.



#### TRUNCATE

```SQL
TRUNCATE TABLE dbtable;
```

- 테이블에 있는 데이터를 한번에 제거한다.
- 테이블이 최초 생성되었을 당시의 storage만 남기고, 데이터가 담겨있던 storage는 release 된다.
  - CREATE TABLE 한 직후 상태로 만들 수 있다.
- TRUNCATE문은 자동 COMMIT 되는 명령어이므로, 이미 지운 데이터를 되돌릴 수 없다



#### DROP TABLE

```SQL
DROP TABLE dbtable;
```

- 테이블 자체를 완전히 날리는 명령어
- 다시 그 테이블을 사용하려면, CREATE TABLE을 다시 해야한다.
- 자동 COMMIT 되므로, 명령 실행 후 되돌릴 수 없다



|                    |      DELETE      |           TRUNCATE            |          DROP          |
| :----------------: | :--------------: | :---------------------------: | :--------------------: |
|    명령어 종류     |       DML        |              DDL              |          DDL           |
|     처리 속도      |       느림       |             빠름              |          빠름          |
|       commit       |  사용자가 직접   |             자동              |          자동          |
| Rollback 가능 여부 | commit 이전 가능 |             불가              |          불가          |
|   삭제되는 정도    |  데이터만 삭제   | 테이블을 CREATE 상태로 되돌림 | 테이블까지 완전히 제거 |

