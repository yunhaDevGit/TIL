# COMMIT, ROLLBACK

#### COMMIT

- DML 언어를 사용할 때는 항상 COMMIT도 함께 해줘야 한다
- 모든 작업을 정상적으로 처리하겠다고 확정하는 명령어
- 트랜젝션의 처리 과정을 데이터베이스에 반영하기 위해서, **변경된 내용을 모두 영구 저장**한다
- COMMIT 수행을 하면 **하나의 트랜젝션 과정을 종료하게 된다**
- 이전 데이터가 완전히 UPDATE 된다.

<img src="https://wikidocs.net/images/page/4096/1.PNG" alt="img" style="zoom: 67%;" />



#### autocommit

mariaDB, MySQL에서 autocommit 확인

```sql
show variables like 'autocommit%';
```

-> 결과가 "ON"이면 autocommit이다.



**autocommit 설정** 

- 세션단에서의 autocommit 설정

```sql
SET AUTOCOMMIT = TRUE;   // 설정
SET AUTOCOMMIT = FALSE;  // 해제
```



- DB 기본 세팅에서의 autocommit 설정

```command
/etc/my.cnf.d/server.cnf

[mysqld]
autocommit=0  // autocommit 해제
```

​	



#### ROLLBACK

- 원래의 상태로 복구하기 위한 명령
- 작업 중 문제가 발생했을 때, 트랜젝션의 처리 과정에서 발생한 변경 사항을 취소하고, 트랜젝션 과정을 종료시킨다.
- 트랜젝션으로 인한 **하나의 묶음 처리가 시작되기 이전의 상태로 되돌린다**
- TRANSACTION(INSERT, UPDATE, DELETE) 작업 내용을 취소한다.
- 이전 COMMIT 한 곳까지 복구한다.

<img src="https://wikidocs.net/images/page/4096/2.PNG" alt="img" style="zoom:67%;" />

