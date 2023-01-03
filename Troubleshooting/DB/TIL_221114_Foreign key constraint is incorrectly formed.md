# errno: 150 "Foreign key constraint is incorrectly formed"

create table 쿼리를 실행하는데 계속해서 아래와 같이 `errno: 150 "Foreign key constraint is incorrectly formed"`에러가 발생했다. 

해당 에러가 발생하는 경우는 보통 아래와 같은 이유가 있다고 한다. 

1. 참조하는 데이터 타입이 다른 경우
2. NOT NULL, NULL 여부가 다른 경우
3. 참조 받는 데이터가 unique key/primary key가 아닌 경우
4. 두 테이블의 charset이 다른 경우

나의 경우는 두 테이블의 charset이 달랐다. 

참조 되는 테이블에는 `default character set utf8;` 설정을 해주었는데, 참조하는 테이블에는 해당 설정을 빼먹어서 생긴 문제였다.
