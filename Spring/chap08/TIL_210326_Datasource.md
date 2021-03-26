# Datasource

- java에서 **connection pool**을 지원하기 위한 인터페이스
  
  - connection pool : connection 맺고 끊는 작업을 줄이기 위해 미리 connection을 생성해두고 모아두는 장소
  
- 순수 jdbc로 데이터베이스에 접근을 하면, 데이터베이스에 접근할 때마다 connection을 맺고 끊는 작업을 한다.

  ```
  순수 JDBC 단점
  > DB프로그램에서 트랜잭션 처리와 Connection 관리는 시스템의 성능과 안전성에 큰 영향을 준다
  > Connection 과정은 일정 시간이 필요한 부담과 작업
  > 불필요한 연결에 의한 서버 자원의 낭비를 발생한다
  ```



DataSource 설정

```java
@Configuration
public class DBConfig {

    @Bean(destroyMethod = "close")
    public DataSource dataSource(){
        DataSource dataSource = new DataSource();
        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost/spring5fs?characterEncoding=utf8");
        dataSource.setUsername("spring5");
        dataSource.setPassword("spring5");
        dataSource.setInitialSize(2);
        dataSource.setMaxActive(10);
        return dataSource;
    }

    @Bean
    public MemberDao memberDao() {
        return new MemberDao(dataSource());
    }
}
```

```java
public class MemberDao {

    private JdbcTemplate jdbcTemplate;

    public MemberDao(DataSource dataSource){
        this.jdbcTemplate = new JdbcTemplate(dataSource);
    }
    
    public Member selectByEmail(String email) {
        List<Member> results = jdbcTemplate.query(
                "select * from MEMBER where EMAIL = ?",
                new MemberRowMapper(), email);
        return results.isEmpty() ? null : results.get(0);
    }
}
```

- bean으로 등록한 DataSource를 MemberDao에 의존성 주입을 해준다
- MemberDao에서는 Sprind JDBC 접근 방법 중 하나인 JdbcTemplate 객체를 생성해서 dataSource를 주입한다. 
- CRUD API를 제공
  - sql문 작성
  - RowMapper interface 구현을 통해 SQL의 결과를 객체에 매핑하여 결과를 리턴한다.
    - 개발자는 mapRow()라는 interface method를 익명 클래스로 작성하여 사용한다.
    - 한 번만 사용하는 기능의 경우 RowMapper를 익명 클래스로 작성하여 사용한다.

