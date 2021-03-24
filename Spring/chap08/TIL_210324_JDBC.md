# JDBC

- Java Database Connectivity
- 자바 프로그램이 데이터베이스와 연결되어 데이터를 주고 받을 수 있게 해주는 프로그래밍 인터페이스
- 통역자의 역할 : 응용프로그램과 DBMS간의 통신을 중간에서 번역해주는 역할을 한다.

<img src="https://lh6.googleusercontent.com/olRNkAgxHh68DT6UFevWdPSmCvOrPTbIWvcJtQ0mD0K1uU7pVM5i3j70nN_D86xsHYDnEhi-_srV9mtFyQOmGvt5G3xyZXAm9k0DE-Cb2nRDwbgmfa6EwhbJvzSfNAZVblXQwoUC" alt="img" style="zoom:80%;" />

<img src="https://mblogthumb-phinf.pstatic.net/20110621_124/james2021_13086434468897j8Oq_JPEG/jdbc.jpg?type=w2" alt="img" style="zoom:80%;" />



JDBC API가 없을 때는 각각의 DB마다 각각의 sql문을 사용했었다. 하지만 JDBC API를 사용할 경우 DBMS에 알맞는 JDBC 드라이버만 있다면 어떤 DB라도 사용이 가능하다.

- 주요 DBMS가 자신에게 알맞는 JDBC 드라이버를 제공한다.



**JDBC API**

- java.sql와 javax.sql로 구성되어 있다.
- 데이터베이스를 연결하여 테이블 형태의 자료를 참조
- sql 문을 질의
- sql 문의 결과를 처리



**JDBC Driver manager**

- 자바 프로그램과 JDBC 드라이버와의 접속(Connection)을 공급하는 역할
- 데이터베이스 드라이버들을 로딩하고, 데이터베이스 연결에 관해 책임지는 클래스
- db에 맞는 드라이버를 load하여 jdbc를 초기화



**JDBC Driver API**

- JDBC Driver manager와 각 DBMS의 벤더에서 제공하는 JDBC Driver가 서로 접속하기 위한 인터페이스



**JDBC Driver**

- 자바 어플리케이션의 요청들을 DBMS가 이해할 수 있는 프로토콜로 변환해준다.



### JDBC 사용 이유

1. 코드 레벨에서 데이터베이스를 조작할 수 있다.

   - 사용자들이 데이터베이스와 간편히 작업할 수 있도록 만들어진 UI는 코드로 작성되는데. 이때 코드 레벨에서 데이터베이스를 사용하기 위한 라이브러리 즉, **쿼리 실행도구 API가 필요**한데 그 중 JAVA에서 사용하는 것이 **JDBC**이다

   

2. 코드 레벨에서 데이터베이스와 관련된 코드 작성을 간편하게 한다

   - query를 작성할 때, 데이터베이스 종류마다 함수명이나 명령문이 다른 경우가 있는데 그런 내용들을 단일화 해주고 작업을 편리하게 해주기 위해 JDBC를 사용한다.

     - JDBC를 이용하기 위해서는 해당 데이터베이스의 드라이버를 다운 받아서 사용해야 한다.

        (JDBC는 단순히 convert 작업만 할 뿐 실제 코드는 데이터베이스에 있기 떄문에 실제 코드가 담긴 드라이버를 라이브러리로 적용해야 한다.)





#### java.sql  Package

1) java.sql.Driver

- DB와 연결하는 Driver class를 만들 때 반드시 implements 해야 하는 interface
- JDBC 드라이버의 중심이되는 인터페이스



2) java.sql.Connection

- 특정 데이터베이스와 연결정보를 가지는 interface
- DriverManager로부터 Connection 객체를 가져온다
  - DriverManager는 JDBC 드라이버를 통해 **커넥션을 만드는 역할**을 한다.



3) java.sql.Statement

- sql query문을 DB에 전송하는 방법을 정의한 interface

- Connection을 통해 가져온다

  

4) java.sql.ResultSet

- SELECT 구문 실행 결과를 조회할 수 있는 방법을 정의한 interface



5) java.sql.PreparedStatement

- Statement의 하위 interface
- SQL 문을 미리 컴파일하여 실행 속도를 높인다



6) java.sql.CallableStatement

- PreparedStatement의 하위 interface
- DBMS의 Stored procedure을 호출



https://docs.oracle.com/javase/8/docs/technotes/guides/jdbc/