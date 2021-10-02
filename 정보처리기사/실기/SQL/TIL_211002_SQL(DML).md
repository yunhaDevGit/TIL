# SQL(DML)

### DML (Data Manipulation Language, 데이터 조작어)

- 저장된 데이터를 실질적으로 관리하는데 사용되는 언어
- SELECT, INSERT, DELETE, UPDATE



### 삽입문 (INSERT INTO~)

```SQL
INSERT INTO 테이블명[속성명1, 속성명2...]
VALUES(데이터1, 데이터2...)
```

- [사원] 테이블에 이름-홍승현, 부서-인터넷을 삽입

  ```SQL
  INSERT INTO 사원(이름, 부서)
  VALUES(홍승현, 인터넷);
  ```



### 삭제문(DELETE FROM~)

```SQL
DELETE 
FROM 테이블명
[WHERE 조건]
```

- [사원] 테이블에서 '인터넷' 부서에 대한 모든 튜플을 삭제하시오

  ```SQL
  DELETE FROM 사원
  WHERE 부서='인터넷';
  ```



### 갱신문 (UPDATE~ SET~)

```SQL
UPDATE 테이블명
SET 속성명=데이터 [,속성명 = 데이터...]
[WHERE 조건];
```

- [사원] 테이블에서 '황진이'의 '부서'를 '기획부'로 변경하고 '기본급'을 5만원 인상

  ```SQL
  UPDATE 사원
  SET 부서='기획부', 기본급=기본급+5
  WHERE 이름='황진이';
  ```

  
