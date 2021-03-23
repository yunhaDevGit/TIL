# JdbcTemplate을 이용한 쿼리 실행

- 스프링을 사용하면 DataSource나 Connection, Statement, ResultSet을 직접 사용하지 않고 JdbcTemplate을 이용해 편리하게 쿼리를 실행할 수 있다.



#### JdbcTemplate 생성

```java
public class MemberDao {

    private JdbcTemplate jdbcTemplate;

    public MemberDao(DataSource dataSource){
        this.jdbcTemplate = new JdbcTemplate(dataSource);
    }  
}
```

- 생성자에 DataSource를 전달하면 된다.

##### 스프링 설정에 MemberDao 빈 설정 추가

```java
@Configuration
public class DBConfig {

    @Bean(destroyMethod = "close")
    public DataSource dataSource(){
        DataSource dataSource = new DataSource();
        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        ...
        return dataSource;
    }

    @Bean
    public MemberDao memberDao() {
        return new MemberDao(dataSource());
    }
}
```

#### JdbcTemplate을 이용한 조회 쿼리 실행

- List<T> query(String sql, RowMapper<T> rowMapper)
- List<T> query(String sql, Object[] args, RowMapper<T> rowMapper)
- List<T> query(String sql, RowMapper<T> rowMapper, Object ... args)

query() 메서드는 sql 파라미터로 전달받은 쿼리를 실행하고 RowMapper를 이용해서 ResultSet의 결과를 **자바 객체로 반환**한다.

```java
// RowMapper의 mapRow() 메서드는 SQL 실행결과로 구한 ResultSet에서 한 행의 데이터를 읽어와 자바 객체로 변환하는 매퍼 기능을 구현한다.
public interface RowMapper<T> {
    T mapRow(ResultSet rs, int rowNum) throws SQLException;
}
```

```java
public Member selectByEmail(String email){
    List<Member> results = jdbcTemplate.query(
        "select * from MEMBER where EMAIL = ?",
        new RowMapper<Member>() {
            @Override
            public Member mapRow(ResultSet rs, int rowNum) throws SQLException {
                Member member = new Member(
                    rs.getString("EMAIL"),
                    rs.getString("PASSWORD"),
                    rs.getString("NAME"),
                    rs.getTimestamp("REGDATE").toLocalDateTime());
                member.setId(rs.getLong("ID"));
                return member;
            }
        }
        ,email); // email은 앞의 sql의 파라미터이다. 
    return null;
}
```



```java
List<Member> results = jdbcTemplate.query(
    "select * from MEMBER where EMAIL = ? and NAME = ?",
    new RowMapper<Member>(){...},
    email, name); // 물음표 개수만큼 해당되는 값 전달
```

람다를 사용하면 더 간단해진다

```java
List<Member> results = jdbcTemplate.query(
    "select * from MEMBER where EMAIL = ?",
    (ResultSet rs, int rowNum) -> {
        Member member = new Member(
            rs.getString("EMAIL"),
            ...
        );
        member.setId(rs.getLong("ID"));
        return member;
    },
email);
```



동일한 RowMapper 구현을 여러 곳에서 사용한다면 RowMapper 인터페이스를 구현한 클래스를 만들어서 코드 중복을 막을 수 있다.

```java
//RowMapper를 구현한 클래스 작성
public class MemberRowMapper implements RowMapper<Member>{
    @Override
    public Member mapRow(ResultSet rs, int rowNum) throws SQLException{
        ...
    }
    
    // MemberRowMapper 객체 생성
    List<Member> results = jdbcTemplate.query(
        "select * from MEMBER where EMAIL = ? and NAME = ?",
        new MemberRowMapper(),
    email, name);
}
```

-> 코드도 간결해지고 중복되는 코드도 제거할 수 있다.