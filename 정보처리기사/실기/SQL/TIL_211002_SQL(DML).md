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

  

### 조회문(SELECT~ FROM~)

```SQL
SELECT [PREDICATE] [테이블명] 속성명 [AS 별칭][,[테이블명,]속성명,...]
	[,그룹함수(속성명) [AS 별칭]]
	[,Window 함수 OVER (PARTITION BY 속성명, 속성명)]
FROM 테이블명[, 테이블명]
[WHERE 조건]
[GROUP BY 속성명, 속성명,,,]
[HAVING 조건]
[ORDER BY 속성명 [ASC|DESC]]
```

- PREDICATE : 검색할 튜프 수를 제한하는 명령어
  - DISTINCT : 중복된 값이 있을 경우 첫번째만 보여줌



#### 조건 연산자

- 비교 연산자 : =, <>, >, <, >=, <=
- 논리 연산자 : NOT, AND, OR
- LIKE 연산자 : %(모든문자 대표), -(문자 하나 대표), #(숫자 하나 대표)



**EXAMPLE**

- [사원] 테이블에서 '주소'만 검색하되 같은 주소는 한번만 출력하시오

  ```SQL
  SELECT DISTINCT FROM 사원;
  ```

- [사원] 테이블에서 생일이 01/01/69에서 12/31/73인사람을 검색하시오

  ```SQL
  SELECT * FROM 사원
  WHERE 생일 BETWEEN #01/01/69# AND #12/31/73#;
  ```

- [사원] 테이블에서 '부서'를 기준으로 오름차순으로 정렬하고, 같은 '부서'에 대해 이름을 기준으로 내림차순 정렬하시오

  ```SQL
  SELECT * FROM 사원
  ORDER BY 부서 ASC, 이름 DESC
  ```

- 취미활동을 하지 않는 사원을 구하시오

  ```SQL
  SELECT * FROM 사원
  WHERE 이름 NOT IN (SELECT 이름 FROM 여가활동)
  ```

  
