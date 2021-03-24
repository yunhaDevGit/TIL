## JDBC 개발 과정

<img src="https://t1.daumcdn.net/cfile/tistory/999BDF4A5C46EF2635" alt="img" style="zoom:80%;" />

1. JDBC Driver Loading

- 데이터베이스 벤더에 맞는 드라이버를 호출한다
- 데이터베이스와의 연결을 위해 드라이버를 로딩
- Class.forName("com.mysql.jdbc.Driver")



2. Connection

- DB와 연결을 위해 URL과 계정 정보가 필요
- 연결 메서드 : DriverManager.getConnection(url, id, pwd) 
  - 실제 자바 프로그램과 데이터베이스를 네트워크 상에서 연결해주는 메소드



3. Statement / PreparedStatement

- SQL 구문을 정의하고 변경 될 값은 치환 문자를 이용해 쿼리 전송 전에 값을 setting한다.

- statement 클래스

  - SQL 구문을 실행하는 역할

  - 스스로는 sql 구문을 해석하지 못하며 전달 역할만 수행

    ```java
    String name = "홍길동";
    String memo = "메모입니다";
    
    String sql = String.format("insert into tblMemo values('%s','%s')", name, memo); 
    ```

- PreparedStatement 클래스

  - Statement 클래스의 기능 향상

  - 인자와 관련된 작업에 특화된다(매개변수)

  - 코드 안정성이 높고, 가독성이 높다

    ```java
    sql = "insert into tblMemo values(?,?)";
    pstat = conn.prepareStatement(sql);
    
    //매개변수 값 대입 + 매개변수 유효화 처리.
    pstat.setString(1, name);
    pstat.setString(2, memo);
    ```



4. executeUpdate() / executeQuery()

- executeUpdate() - sql query문이 INSERT, DELETE, UPDATE의 경우에 사용. 반환값의 타입 int
- executeQuery() - sql query문이 SELECT인 경우 사용. 반환값의 타입 ResultSet



5. ResultSet (SELECT의 경우)

- 데이터베이스 조회 결과에 대한 표준
- next()를 통해 DB의 table 안의 row 한 줄을 불러온다.
- getString(), getInt()를 통해 한 행의 특정 Column 값을 가져온다.



6. close (Connection, Statement, ResultSet)





https://shs2810.tistory.com/18