# JPA 시작하기

JPA를 시작하기 전에 먼저 JPA가 왜 나왔는지에 대해 먼저 보자.

현재 우리는 객체를 관계형 DB에 저장해서 관리한다

객체지향으로 개발한다고 하는데 코드를 까보면 다 sql이다...데이터베이스는 sql만 알아들을 수 있기 때문이다. 

그렇기 때문에 우리는 관계형 데이터베이스가 알아들을 수 있는 sql을 계속 작성해서 날려야 한다.



### SQL 중심적인 개발의 문제점

- 무한 반복, 지루한 코드 (자바 객체를 sql로 바꾸고 sql을 자바 객체로 바꾸고....)

- 컬럼 추가 시, 모델 객체에 컬럼 넣고 sql 문들도 다 변경해야한다.

  - ex)

    ```java
    public class Member {
        private String id;
        private String name;
        ...
    }
    ```

    ```sql
    INSERT INTO MEMBER(ID, NAME) VALUES (...);
    SELECT IS, NAME FROM MEMBER;
    ```

    다음과 같이 모델과 sql문이 작성 되어 있다고 할 때, tel이라는 컬럼을 추가했다고 하자

    ```java
    public class Member {
        private String id;
        private String name;
        private String tel;
        ...
    }
    ```

    ```sql
    INSERT INTO MEMBER(ID, NAME, TEL) VALUES (...);
    SELECT ID, NAME, TEL FROM MEMBER;
    ```

    쿼리들을 맞춰서 일일이 수정을 해주어야 한다. 수정하는 과정에서 빼놓고 하게 될 수도 있다...

- SQL에 의존적인 개발을 피하기가 어렵다
- 패러다임의 불일치
  - 애초에 관계형데이터베이스가 나온 사상과 객체지향이 나온 사상 자체가 다르다
  - 관계형데이터베이스 - 데이터를 잘 정규화해서 보관을 하는게 목표
  - 객체 - 속성과 기능이 묶여서 잘 캡슐화해서 사용하는게 목표
    - 추상화, 캡슐화, 정보은닉, 상속, 다형성 등 시스템의 복잡성을 제어할 수 있는 다양한 장치들을 제공한다
- 엔티티 신뢰 문제



#### 객체와 관계형 데이터베이스의 차이

- 상속

  - 객체는 상속 관계가 있다

- 연관관계

  - 객체는 레퍼런스(참조)를 가지고 연관된 객체를 가지고 올 수 있다

  - 관계형 데이터베이스는 외래키를 사용하여 join을 해서 필요한 데이터를 찾는다

    <img src="https://lh5.googleusercontent.com/6kvEdO8V8Aaa6CjoLnofFIvvXXLX8g9_genUn4E6xrU_rS9YnC9vS8psMR364IKXWDP-gQRB7EszrsgTBeIlTGHEOyT7IyjXjL-b956cieEgp8RSX4eLXyB7ML5D2mU3PJ0yZtSe" alt="img" style="zoom:67%;" />

    - 객체는 한방향으로만 참조할 수 있다. (Member가 Team을 참조할 수 있지만 Team은 Member를 참조할 수 없다)
    - 테이블은 양방향으로 참조할 수 있다. 

- 데이터 타입

- 데이터 식별 방법





**객체답게 모델링 할수록 매핑 작업이 늘어난다**

:question: 객체를 자바 컬렉션에 저장하듯이 DB에 저장할 수 없을까? 이러한 의문으로부터 JPA가 나오게 되었다.

​    (개발자는 객체지향적으로 프로그래밍을 하고, JPA가 이를 관계형 데이터베이스에 맞게 SQL을 대신 생성하여 실행한다)





jpa

- 보안적인 측면으로도 좋다
- 쿼리 부분을 캡슐화하여 안보이게 할 수 있다. 이부분에 대해서는 좀 더 공부해보자



python - django Models