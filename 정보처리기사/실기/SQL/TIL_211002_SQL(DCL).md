# SQL - DCL

### DCL(Data Control Language, 데이터 제어어)

- DCL은 데이터 보안, 무결성, 회복, 병행 제어 등을 정의하는데 사용하는 언어
- COMMIT, ROLLBACK, GRANT, REVOKE



### GRANT / REVOKE

- 데이터베이스 관리자가 사용자에게 권한을 부여하거나 취소하는 명령어
- GRANT : 권한 부여
- REVOKE : 권한 취소
  - 사용자 등급
    - DBA : 데이터베이스 관리자
    - RESOURCE : 데이터베이스 및 테이블 생성 기능자
    - CONNECT : 단순 사용자

```SQL
GRANT 사용자등급 TO 사용자_ID_리스트 [IDENTIFIED BY 암호];
REVOKE 사용자등급 FROM 사용자_ID_리스트;
```

- 사용자 ID가 'NABI'인 사람에게 데이터베이스 및 테이블을 생성할 수 있는 권한 부여

```SQL
GRANT RESOURCE TO NABI;
```



#### 테이블 및 속성에 대한 권한 부여 및 취소

```SQL
GRANT 권한_리스트 ON 개체 TO 사용자 [WITH GRANT OPTION];
REVOKE [GRANT OPTION FOR] 권한_리스트 ON 개체 FROM 사용자 [CASCADE];
```

- 권한 종류
  - ALL, SELECT, INSERT, DELETE, UPDATE, ALTER 등
  - WITH GRANT OPTION : 부여받은 권한을 다른 사용자에게 부여할 수 있는 권한 부여
  - GRANT OPTION FOR : 다른 사용자에게 권한 부여할 수 있는 권한 취소
  - CASCADE : 권한 취소 시 사용자가 다른 사용자에게 부여한 권한도 연쇄적으로 취소

- 사용자 ID가 NABI인 사람에게 [고객] 테이블에 대한 모든 권한과 다른 사람에게 권한을 부여할 수 있는 권한 부여

  ```SQL
  GRANT ALL ON 고객 TO NABI WITH GRANT OPTION;
  ```



### COMMIT

- 트랜잭션이 수행한 내용을 데이터베이스에 반영하는 명령



### ROLLBACK

- 변경되었으나 아직 COMMIT 되지 않은 모든 내용들을 취소하고 데이터 베이스를 이전 상태로 되돌리는 명령
- 트랜잭션 전체가 성공적으로 끝나지 못하면 일부 변경된 내용만 데이터베이스에 반영되는 비일관성 상태가 될 수 있기 때문에



### SAVEPOINT

- 트랜잭션 내에서 ROLLBACK할 위치인 저장점을 지정하는 명령어

  ```SQL
  SAVEPOINT S1;
  DELETE FROM 어쩌구1...;
  SAVEPOINT S2;
  DELETE FROM 어쩌구2...;
  ROLLBACK TO S2;
  -> DELETE FROM S2 하기 전으로 돌아간다.
  ```

  
