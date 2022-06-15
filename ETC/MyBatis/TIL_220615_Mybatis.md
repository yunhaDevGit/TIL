# Mybatis

Mybatis는 Sql Mapper 중 하나로 jdbc로 처리하는 코드의 설정(connection) 부분을 줄이고 실제 sql 문에 연결함으로써 빠른 개발을 가능하게 해줍니다. Mybatis에서는 프로그램에 있는 sql 쿼리들을 한 구성 파일에 구성하여 프로그램 코드와 sql을 분리할 수 있는 장점을 가지고 있습니다. 

## 특징

복잡한 쿼리나 다이나믹한 쿼리에 유용합니다. 반대로 비슷한 쿼리는 남발하게 되는 단점이 있습니다. 또한 프로그램 코드와 SQL 코드의 분리로 코드의 간결성 및 유지 보수성이 향상 됩니다. 

## MyBatis3 구성 요소

**Mybatis Configuration file**

- MyBatis3의 작업 설정을 설명하는 XML 파일입니다.
- 데이터베이스의 연결 대상, 매핑 파일 경로, MyBatis3의 작업 설정 등과 같은 세부 사항을 설정합니다.
- **DataSoruce**
    - JDBC 사용 시 데이터베이스에 접근할 때마다 Connection을 연결하고 끊어주었습니다. 이 Connection 작업을 줄이기 위해 미리 Connection을 생성해서 Connection Pool에 저장하기로 했고 DB에 접근할 때마다 Connection Pool에서 미리 생성된 Connection을 제공 받고 다시 돌려주었습니다. 
    DataSource는 자바에서 Connection Pool을 지원해주는 인터페이스입니다.
        
        ```yaml
        spring.datasource.driver-class-name = 드라이버명
        spring.datasource.url = url입력
        spring.datasource.username = 유저명
        spring.datasource.password = 비밀번호
        ```
        

**SqlSessionFactoryBuilder**

- MyBatis3 구성 파일을 읽고 SqlSessionFactory를 생성하는 구성 요소입니다.

**SqlSessionFactory**

- SqlSession을 생성하는 구성 요소입니다.

**SqlSession**

- SQL 실행 및 트랜잭션 제어를 위한 API를 제공하는 구성 요소입니다.
- MyBatis3를 사용하여 데이터베이스에 액세스 할 때 가장 중요한 역할을 하는 구성 요소입니다.

**Mapper interface**

- typeafe에서 매핑 파일에 정의된 SQL을 호출하는 인터페이스입니다.
- MyBatis3는 매퍼 인터페이스에 대한 구현 클래스를 자동으로 생성하므로 개발자는 인터페이스만 생성하면 됩니다.

**Mapping file**

- SQL 및 O/R 매핑 설정을 설명하는 XML 파일입니다.

### MyBatis3 구성 요소가 Database Access 하는 순서

![image](https://user-images.githubusercontent.com/74949294/173783243-691bd449-4006-4378-ba65-a1be3dce7af7.png)

**(1) ~ (3)은 응용 프로그램 시작 시 수행되는 프로세스입니다**

(1) 응용 프로그램이 `SqlSessionFactoryBuilder`를 위해 `SqlSessionFactory`를 빌드하도록 요청합니다.

(2) `SqlSessionFactoryBuilder`는 `SqlSessionFactory`를 생성하기 위한 MyBatis 구성 파일을 읽습니다.

(3) `SqlSessionFactoryBuilder`는 MyBatis 구성 파일의 정의에 따라 `SqlSessionFactory`를 생성합니다.

**(4) ~ (10)은 클라이언트의 각 요청에 대해 수행되는 프로세스입니다**

(4) 클라이언트가 응용 프로그램에 대한 프로세스를 요청합니다. 

(5) 응용 프로그램은 `SqlSessionFactoryBuilder`를 사용하여 빌드 된 `SqlSessionFactory`에서 `SqlSession`을 가져옵니다. 

(6) `SqlSessionFactory`는 `SqlSession`을 생성하고 이를 애플리케이션에 반환합니다. 

(7) 응용 프로그램이 `SqlSession`에서 매퍼 인터페이스의 구현 개체를 가져옵니다. 

(8) 응용 프로그램이 매퍼 인터페이스 메서드를 호출합니다. 

(9) 매퍼 인터페이스의 구현 개체가 `SqlSession`  메서드를 호출하고 SQL 실행을 요청합니다. 

(10) `SqlSession`은 매핑 파일에서 실행할 SQL을 가져와 SQL을 실행합니다. 

## MyBatis-Spring의 컴포넌트 구조

**SqlSessionFactoryBean**

- SqlSessionFactory를 작성하고 Spring DI 컨테이너에 개체를 저장하는 구성 요소
- 표준 MyBatis3에서 SqlSessionFactory는 MyBatis 구성 파일에 정의된 정보를 기반으로 합니다. 그러나 SqlSessionFactoryBean을 사용하면 MyBatis 구성 파일이 없어도 SqlSessionFactory를 빌드 할 수 있습니다.

**MapperFactoryBean**

- Singleton Mapper 개체를 만들고 Spring DI 컨테이너에 개체를 저장하는 구성 요소
- MyBatis3 표준 메커니즘에 의해 생성된 매퍼 객체는 Thread-safe 하지 않습니다. 따라서 각 스레드에 대한 인스턴스를 할당해야 했습니다. Mybatis-spring 구성 요소에 의해 생성된 매퍼 개체는 안전한 매퍼 개체를 생성할 수 있습니다. 따라서 서비스 등 싱글톤 구성 요소에 DI를 적용할 수 있습니다.

**SqlSessionTemplate**

- SqlSession 인터페이스를 구현하는 싱글톤 버전의 SqlSession 구성 요소
- MyBatis3 표준 메커니즘에 의해 생성된 매퍼 객체는 Thread-safe 하지 않습니다. 따라서 각 스레드에 대한 인스턴스를 할당해야 했습니다. Mybatis-spring 구성 요소에 의해 생성된 SqlSession 개체는 안전한 SqlSession 개체를 생성할 수 있습니다. 따라서 서비스 등 싱글톤 구성 요소에 DI를 적용할 수 있습니다.

 
![image](https://user-images.githubusercontent.com/74949294/173783271-f45c5a6b-8be5-4873-87c5-09fc6da9a780.png)

**(1) ~ (4)은 응용 프로그램 시작 시 수행되는 프로세스입니다**

(1) `SqlSessionFactoryBean`은 `SqlSessionFactoryBuilder`에 `SqlSessionFactory`를 빌드 하도록 요청합니다.

(2) `SqlSessionBuilder`는 `SqlSessionFactory` 생성을 위해 MyBatis 구성 파일을 읽습니다. 

(3) `SqlSessionFactoryBuilder`는 MyBatis 구성 파일의 정의에 따라 `SqlSessionFactory`를 생성합니다. 이렇게 생성된 SqlSessionFactory는 Spring DI 컨테이너에 의해 저장됩니다.

(4) `MapperFactoryBean`은 Thread-safe `SqlSession (SqlSessionTemplate)`과 Thread-safe `Mapper 객체`를 생성 합니다. 따라서 생성되는 매퍼 객체는 Spring DI 컨테이너에 의해 저장되며 서비스 클래스 등에 DI가 적용됩니다. 

**(5) ~ (11)은 클라이언트의 각 요청에 대해 수행되는 프로세스입니다**

(5) 클라이언트가 응용 프로그램에 대한 프로세스를 요청합니다.

(6) 애플리케이션(서비스)는 DI 컨테이너에서 주입한 매퍼 개체(매퍼 인터페이스를 구현하는 프록시 개체)의 방법을 호출합니다. 

(7) 매퍼 객체는 호출된 메소드에 해당하는 `SqlSession(SqlSessionTemplate)` 메서드를 호출합니다. 

(8) `SqlSession (SqlSessionTemplate)`은 프록시 사용 및 안전한 SqlSession 메서드를 호출합니다. 

(9) 프록시 사용 및 스레드 안전 SqlSession은 트랜잭션에 할당된 MyBatis3 표준 SqlSession을 사용합니다. 트랜잭션에 할당 된 SqlSession이 존재하지 않는 경우 SqlSessionFactory 메서드를 호출하여 표준 MyBatis3의 SqlSession을 가져옵니다. 

(10) `SqlSessionFactory`는 MyBatis3 표준 `SqlSession`을 반환합니다 

  반환된 MyBatis3 표준 SqlSession이 트랜잭션에 할당 되기 때문에 동일한 트랜잭션 내에 있는 경우 새 SqlSession을 생성하지 않고 동일한 SqlSession을 사용합니다. on 메서드를 호출하고 SQL 실행을 요청합니다. 

(11) MyBatis3 표준 `SqlSession`은 매핑 파일에서 실행할 SQL을 가져와 실행합니다.
