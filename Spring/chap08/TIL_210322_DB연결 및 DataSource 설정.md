## DB 테이블 생성

```sql
create user 'spring5'@'localhost' identified by 'spring5';

create database spring5fs character set=utf8;

grant all privileges on spring5fs.* to 'spring5'@'localhost';

create table spring5fs.MEMBER (
    ID int auto_increment primary key,
    EMAIL varchar(255),
    PASSWORD varchar(100),
    NAME varchar(100),
    REGDATE datetime,
    unique key(EMAIL)
) engine=INNODB character set=utf8;

insert into MEMBER (EMAIL, PASSWORD, NAME, REGDATE)
values ('hello@hello.com', '1234', 'yunha', now());
```

- spring5 user 계정 생성 및 권한 부여
- MEMBER  table 생성
- 데이터 삽입



## DataSource 설정

JDBC API는 DriverManager 외에 DataSource를 이용해서 DB 연결을 구하는 방법을 정의.

DataSource를 사용하면 아래와 같이 Connection을 구할 수 있다.

```java
Connection conn = null;
try{
    //dataSource는 생성자나 설정 메서드를 이용해서 주입받는다.
    conn = dataSource.getConnection();
    ...
}
```

스프링이 제공하는 DB 연동 기능은 DataSource를 사용해서 DB Connection을 구한다.

DB 연동에 사용할 DataSource를 스프링 빈으로 등록하고 DB 연동 기능을 구현한 빈 객체는 DataSource를 주입받아 사용한다.

Tomcat JDBC - javax.sql.DataSource를 구현한 DataSource 클래스 제공

```java
import org.apache.tomcat.jdbc.pool.DataSource;

@Configuration
public class DBConfig {
    
    // close 속성 : 커넥션 풀에 보관된 connection을 닫는다
    @Bean(destroyMethod = "close") 
    public DataSource dataSource(){
        // DataSource 객체 생성
        DataSource dataSource = new DataSource();
        // JDBC Driver 클래스 지정. MySQL 드라이버 사용
        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost/spring5fs?characterEncoding=utf8");
        dataSource.setUsername("spring5");
        dataSource.setPassword("spring5");
        dataSource.setInitialSize(2);
        dataSource.setMaxActive(10);
        return dataSource;
    }
}
```

org.apache.tomcat.jdbc.pool.DataSource : 커넥션 풀을 제공하는 DataSource 구현 클래스



|             설정 메서드              |                             설명                             |
| :----------------------------------: | :----------------------------------------------------------: |
|         setInitialSize(int)          | 커넥션 풀을 초기화할 때 생성할 초기 커넥션 개수를 지정. 기본값 10 |
|          setMaxActive(int)           | 커넥션 풀에서 가져올 수 있는 최대 커넥션 개수를 지정. 기본값 100 |
|           setMaxIdle(int)            | 커넥션 풀에서 유지할 수 있는 최대 커넥션 개수를 지정. 기본값 maxActive와 같다 |
|           setMinIdle(int)            | 커넥션 풀에서 유지할 최소 커넥션 개수를 지정. 기본값 initialSize와 같다 |
|           setMaxWait(int)            | 커넥션 풀에서 커넥션을 가져올 때 대기할 최대 시간을 밀리초 단위로 지정. 기본값 30000밀리초(30초) |
|           setMaxAge(long)            | 최초 커넥션 연결 후 커넥션의 최대 유효시간을 밀리초 단위로 지정. 기본값 0 : 유효시간 없음 |
|      setValidationQuery(String)      | 커넥션이 유효한지 검사할 때 사용할 쿼리 지정. 기본값 null : 검사 하지 않는다. |
|    setValidationQueryTimeout(int)    | 검사 쿼리의 최대 실행시간을 초 단위로 지정. 기본값 -1 : 0이하면 검사 비활성화 |
|       setTestOnBorrow(boolean)       |   풀에서 커넥션을 가져올 때  검사 여부 지정. 기본값 false    |
|       setTestOnReturn(boolean)       |    풀에서 커넥션을 반환할 때 검사 여부 지정. 기본값 false    |
|      setTestWhileIdle(boolean)       | 커넥션이 풀에 유휴 상태로 있는 동안 검사 여부 지정. 기본값 false |
|    setMinEvictableTimeMills(int)     | 커넥션 풀에 유휴 상태로 유지할 최소 시간을 밀리초 단위로 지정.  testWhileIdle이 true이면 유휴 시간이 이 값을 초과한 커넥션 풀에서 제거한다. 기본값 60000밀리초(60초) |
| setTimeBetweenEvictionRunsMills(int) | 커넥션 풀의 유휴 커넥션을 검사할 주기를 밀리초 단위로 지정. 기본값 5000밀리초(5초) |

**커넥션 풀에 커넥션 요청 : 활성화(active)**

**커넥션 풀에 커넥션 반환 : 유휴(idle)**



```JAVA
@Bean(destroyMethod="close")
public DataSource dataSource(){
	DataSource ds = new DataSource();
    ds.setDriverClassName("com.mysql.jdbc.Driver");
    ds.setUrl("jdbc:mysql://localhost/spring5fs?characterEncoding=utf8");
    ds.setUserName("spring5");
    ds.setPassword("spring5");
    ds.setInitialSize(2);
    ds.setMaxActive(10);
    ds.setTestWhileIdle(true); // 유휴 커넥션 검사
    ds.setMinEvictableIdleTimeMillis(1000*60*3); // 최소 유휴 시간 3분
    return ds;
}
```



***커넥션 풀 사용 이유***

- 매번 새로운 커넥션을 생성하면 그때마다 연결 시간이 소모된다 -> *성능적인 측면*