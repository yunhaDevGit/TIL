# SQL - DDL

### DDL (Data Defined Language)

- DB를 구축하거나 수정할 목적으로 사용하는 언어
- CREATE, ALTER, DROP



### CREATE  SCHEMA - 스키마 정의

```sql
CREATE SCHEMA 스키마명 AUTHORIZATION 사용자_ID;
```

- 소유권자의 사용자 ID가 '홍길동'인 스키마 '대학교를 정의하는 SQL문'

  ```SQL
  CREATE SCHEMA 대학교 AUTHORIZATION '홍길동';
  ```



### CREATE  DOMAIN - 도메인 정의

```SQL
CREATE DOMAIN 도메인명 [AS] 데이터타입
	[DEFAULT 기본값] 
	[CONSTRAINT 제약조건명 CHECK (범위값)];
```

- '성별'을 '남' 또는 '여'와 같이 정해진 1개의 문자로 표현되는 도메인 SEX를 정의하는 SQL문

  ```SQL
  CREATE DOMAIN SEX CHAR(1)
  	DEFAULT '남'
  	CONSTRAINT VALID_SEX CHECK(VALUE IN('남', '여'));
  ```



### CREATE TABLE - 테이블 정의

```SQL
CREATE TABLE 테이블명
	(속성명 데이터_타입 [DEFAULT 기본값][NOT NULL]...)
	[,PRIMARY KEY(기본키_속성영,...)]
	[,UNIQUE(대체키_속성명...)]
	[,FOREIGN KEY(외래키_속성명...)]
		[REFERENCES 참조테이블(기본키_속성명)]
		[ON DELETE 옵션]
		[ON UPDATE 옵션]
	[,CONSTRAINT 제약조건명][CHECK (조건식)]
```

- 이름, 학번, 전공, 성별, 생년월일로 구성된 [학생] 테이블을 정의하는 SQL문

  - '이름'은 NULL이 올 수 없고, '학번'은 기본키이다
  - '전공'은 [학과]테이블의 '학과코드'를 참조하는 외래키로 사용된다
  - [학과] 테이블에서 삭제가 일어나면 관련된 튜플들의 전공 값을 NULL로 만든다
  - [학과] 테이블에서 '학과코드'가 변경되면 전공 값도 같은 값으로 변경한다
  - '생년월일'은 1980-01-01 이후의 데이터만 저장할 수 있다
  - 제약 조건의 이름은 '생년월일제약'으로 한다
  - 각 속성의 데이터 타입은 적당히 지정하고, '성별'은 도메인 'SEX'를 사용한다

  ```SQL
  CREATE TABLE 학생
  	(이름 VARCHAR(10) NOT NULL,
      학번 CHAR(8) PRIMARY KEY,
      전공 CHAR(5),
      성별 SEX,
      생년월일 DATE,
      FOREIGN KEY(전공) REFERENCES 학과(학과코드)
      	ON DELETE SET NULL
      	ON UPDATE CASCADE,
      CONSTRAINT 생년월일제약 CHECK(생년월일>='1980-01-01'));
  ```

  

### CREATE VIEW - 뷰를 정의하는 명령문

```SQL
CREATE VIEW 뷰명[속성명[,속성명,...]]
AS SELECT 문
```

- [고객] 테이블에서 '주소'가 '안산시'인 고객들의 '성명'과 '전화번호'를 '안산고객'이라는 뷰로 정의

  ```SQL
  CREATE VIEW 안산고객(성명, 전화번호)
  AS SELECT 성명, 전화번호
  FROM 고객
  WHERE 주소='안산시';
  ```



### CREATE INDEX - 인덱스를 정의하는 명령문

```SQL
CREATE [UNIQUE] INDEX 인덱스명
ON 테이블명(속성명[ASC|DESC], [속성명[ASC|DESC]])
[CLUSTER];
```

- [고객] 테이블에서 UNIQUE한 특성을 갖는  '고객번호' 속성에 대해 내림차순으로 정렬하여 '고객번호_idx'라는 이름으로 인덱스를 정의하시오

  ```sql
  CREATE UNIQUE INDEX 고객번호_idx 
  ON 고객(고객번호 DESC);
  ```



### ALTER TABLE - 테이블에 대한 정의 변경하는 명령문

```SQL
ALTER TABLE 테이블명 ADD 속성명 데이터타입 [DEFAULT '기본값'];
ALTER TABLE 테이블명 UPDATE 속성명 [SET DEFAULT '기본값'];
ALTER TABLE 테이블명 DROP COLUMN 속성명 [CASCADE|RESTRICT];
```



### DROP TABLE - 스키마, 도메인, 테이블, 뷰 테이블 등 제거하는 명령문

```SQL
DROP SCHEMA 스키마명 [CASCADE|RESTRICT];
DROP DOMAIN 도메인명 [CASCADE|RESTRICT];
DROP TABLE 테이블명 [CASCADE|RESTRICT];
DROP VIEW 뷰명 [CASCADE|RESTRICT];
DROP INDEX 인덱스명 [CASCADE|RESTRICT];
DROP CONSTRAINT 제약조건명;
```


