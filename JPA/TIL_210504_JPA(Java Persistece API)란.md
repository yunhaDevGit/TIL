# JPA(Java Persistece API)란

- 자바 진영의 ORM 기술 표준이다



### ORM이란?

- Object-relational mapping(객체 관계 매핑) - 객체와 관계형 데이터베이스를 매핑하는 것

- 객체는 객체대로 설계를 하고, 관계형 데이터베이스는 관계형 데이터베이스로 설계를 한 다음 ORM 프레임워크가 중간에서 매핑해주는 역할을 한다.

  (객체와 관계형 데이터베이스 간의 차이는 ORM 프레임워크가 해결해준다)

  ex) 객체를 데이터베이스에 저장할 때 INSERT SQL을 작성하는 것이 아니라 객체를 마치 자바 컬렉션에 저장하듯이 ORM 프레임워크에 저장하면, ORM 프레임워크가 적절한 INSERT SQL을 생성해서 데이터베이스에 객체를 저장해준다



<img src="https://lh6.googleusercontent.com/fX-LlsfEeAHV8a6EaT2lT4TQMgvxqt0346YRiTbhYRy4ATrHaMrmoLYA-BQe_TrlUPF1s8WJcthmz47IwnKnv8MMEQZ_2ut7wQgD-R1E7YyiSFXQWVfMlos8aelouxwdm_4hv9iN" alt="img" style="zoom:80%;" />

JPA는 애플리케이션과 JDBC 사이에서 동작을 한다.

개발자가 JPA에게 명령을 하면 JPA가 JDBC API를 사용하여 SQL을 호출하고 결과를 반환하여 동작한다.



### JPA 동작 방식

#### 저장

![img](https://lh4.googleusercontent.com/iW33K0xDpjk54UlTbCqlcWIz4w9MuBTmodEf0uaMcExnyOdaXeRSLtRimey22Wliu45c2wyLKVjacBKAuJO4XE3BN0S4iUsGe5ZhQ4lZuuz6ePxZjjLwMhdoxu2cAT62CxG_MypV)

MemberDAO에서 객체를 저장하고 싶다!

과거

- JDBC API나 JDBC Template이나 Mybatis 사용

JPA

- JPA에게 Member 객체를 넘긴다
- JPA가 Member 객체를 분석
- 적절한 Insert 쿼리 생성
- JPA가 JDBC API를 사용하여 Insert 쿼리를 DB에 보낸다
  - 쿼리를 개발자가 아닌 JPA가 만들어준다
  - **패러다임의 불일치를 해결해준다**



#### 조회

![img](https://lh3.googleusercontent.com/pgP8sYHvCoDDTYF7BBrztQbl_9witK5Snf0IAN2EoHDaQOfQuZEAji_OV0gM7NNRb0tLag4GP0Ue-0-3qIOLQ3_lyY9bcwWyNLHScF-0T5w_6znOEVtUpyRPOA2csIj5GttzaRtS)



PK 값만 넘기면 JPA가 Member 객체를 보고 적절한 select문을 만들어준다. 

select 쿼리를 만들어서 JDBC API를 통해 DB에 보낸 후 결과를 받은 다음 ResultSet을 객체로 매핑해준다

여기서도 패러다임의 불일치를 해결해준다





## JPA 소개

과거에는 EJB(엔티티 빈)라는 것을 자바 진영의 표준으로 사용하고 있었다. (아마추어적인 기술이었다)

- 인터페이스를 엄청 구현해야 한다
- 기능도 잘 동작 안함
- 느리다
- 성능 안좋다

-> 거의 잘 안쓰였다



그러다가 EJB를 쓰던 SI 개발자가 '내가 만들어도 이것보다 잘 만들겠다' 싶어서 퇴근하고 만든 ORM 프레임워크가 'Hibernate'이다.

많은 개발자들이 참여하여 Hibernate가 만들어졌고, 오픈소스화 되었다



자바 표준으로 만든 EJB는 완전 망하고 Hibernate가 떴다. 그러다가 자바 진영에서 반성을 하고, Hibernate를 만든 개발자를 데리고 와서 Hibernate와 거의 유사한 형태로 JPA를 만들었다.

이렇게 만들어진 JPA가 자바 표준으로 사용한다.



***JPA는 인터페이스의 모음이다***





### JPA를 왜 사용하는가?

- SQL 중심적인 개발에서 객체 중심으로 개발

- 생산성

  - JPA로 CRUD 하는 방법 (이미 다 만들어져 있고 해당 메서드 불러서 사용하면 된다)

    - 저장 : jpa.persist(member)
    - 조회 : Member member = jpa.find(memberId)
    - 수정 : member.setName("변경할 이름")
    - 삭제 : jpa.remove(member)

  - 수정할 때 setName만 하고 따로 데이터베이스에 넣는 작업을 하지 않아도 수정할 수 있다. 어떻게 그게 가능한거지?

     JPA를 사용하는 것은 마치 데이터를 자바 컬렉션에 데이터를 넣고 빼서 사용하는 것과 같은 거라고 생각하면 된다. 자바 컬렉션에 있는 객체의 이름을 setName으로 변경했을 때 다시 컬렉션에 데이터를 넣지 않는 것과 같은 맥락이다.

- 유지보수

  - 기존 - 필드 변경 시 모든 SQL을 수정해야한다
  - JPA 사용 - DB에 컬럼이 추가된다면 모델 객체에만 해당 컬럼을 추가해주면 된다. 쿼리를 따로 수정할 필요 없다

- 패러다임 불일치 해결

  - JPA 상속

    <img src="https://lh3.googleusercontent.com/SFus4pbOP-LxL51oU5qAaW2YUrMo6vNAs5S9p__BHXMndTuj_8s-2-NauN15hDdM6WNR6f0SHzmEd92ZE96P41N4xyr7FGYwEvr6t8uQ8gXyXmhe-Lw4-P57WisLutOTjKU6tTrb" alt="img" style="zoom: 67%;" />

    Album 객체를 DB에 **저장**하고 싶을 때 

    ```java
    // 개발자가 작성할 코드
    jpa.persist(album);
    ```

    이렇게 작성하면 jpa가 알아서 insert 쿼리를 두개로 쪼개서 만들어준다

    ```sql
    // JPA가 실행하는 코드
    insert into Item ...
    insert into Album ...
    ```

    -> 개발자는 데이터베이스의 구조에 대해 고민하지 않아도 JPA가 알아서 해준다

    

    Album을 **조회**하고 싶을 때

    Album의 PK 값을 넘기면, Item과 Album을 Join해서 데이터를 보여준다.

    ```java
    // 개발자가 작성할 코드
    Album album = jpa.fing(Album.class, albumId);
    ```

    ```sql
    // JPA가 실행할 코드
    select I.*, A.*
    from Item I
    join Album A on I.item_id = A.item_id;
    ```

    

  - JPA 연관관계

    ```java
    member.setTeam(team);
    jpa.persist(member); 
    ```

    멤버가 DB에 저장이 된다

    

  - 객체 그래프 탐색

    ```java
    Member member = jpa.find(Member.class, memberId);
    Team team = member.getTeam();
    ```

    마치 자바 컬렉션에 넣은 것처럼 Team을 꺼내올 수 있다.

    

  - 신뢰할 수 있는 엔티티

    ```java
    class MemberService{
        ...
        public void process() {
            Member member = memberDao.find(memberId);
            member.getTeam();
            member.getOrder().getDelivery();
        }
    }
    ```

    JPA를 사용하지 않는다면 memberDao에서 어떠한 쿼리를 날렸는지에 따라 객체 정보를 탐색할 수 있다.

    즉, 쿼리를 봐야 객체 그래프 탐색 여부를 확인할 수 있다.

    하지만 JPA를 통해 Member 객체를 가지고 오면 객체 그래프를 자유롭게 다 탐색할 수 있다.

    JPA는 지연 로딩이라는 기능이 있기 때문이다.

    > 지연 로딩은 member.getTeam()과 같이 실제 해당 객체를 조회해서 사용하는 시점에 sql 쿼리가 나가 데이터가 처리되는 기능이다

    즉, JPA를 통해 가지고 온 Member 객체는 믿을 수 있다





### JPA의 성능 최적화 기능

1. 1차 캐시와 동일성(identity) 보장

   - 같은 트랜잭션 안에서는 같은 엔티티를 반환해준다 -> **약간의 조회 성능을 향상**시켜준다

     - 비즈니스 레벨이 엄청 복잡할 때, 중간중간 똑같은 멤버를 조회하는 로직이 있을 수 있다. 이렇게 되면 똑같은 sql을 반복적으로 실행해야 한다.

       JPA를 사용하면 **JPA의 캐싱기능을 통해 처음에만 sql을 실행하고, 다음부터는 메모리에 있는 값을 가지고 온다.**

       ```java
       String memberId = "100";
       Member m1 = jpa.find(Member.class, memberId); // sql
       Member m2 = jpa.find(Member.class, memberId); // 캐시
       
       println(m1==m2) // true
       ```

       ***sql은 한번만 실행한다***

       

2. 트랜잭션을 지원하는 쓰기 지원(transactional write-behind) - 버퍼링 기능

   - 트랜잭션을 커밋할 때까지 INSERT SQL을 모은다

     - JPA를 사용하여 옵션을 하나 키면 아래와 같이 사용할 수 있다.

       ```java
       transaction.begin(); // 트랜잭션 시작
       
       em.persist(memberA);
       em.persist(memberB);
       em.persist(memberC);
       // 여기까지 INSERT SQL을 데이터베이스에 보내지 않는다
       
       // 커밋하는 순간 데이터베이스에 INSERT SQL을 모아서 보낸다
       transaction.commit(); // 트랜잭션 커밋
       ```

       memberA, memberB, memberC가 들어오면 메모리에 쌓은 후 커밋되는 순간 세개가 같은 경우엔 쿼리 세개를 한번에 네트워크를 통해 보낸다.

       

   - JDBC BATCH SQL 기능을 사용하여 한번에 SQL 전송

     - 코드가 지저분해진다.

     

3. 지연로딩(Lazy Loading)

   - 지연 로딩 : 객체가 실제로 사용될 때 로딩

     ```java
     Member member = memberDao.find(memberId);  // select * from member
     Team team = member.getTeam();
     String teamName = team.getName(); // select * from team
     ```

     -> 네트워크를 두번 나가야 한다.

     ***만약 member를 조회할 때 항상 team도 같이 조회해야 한다면 두번보다는 한번에 조회하는 것이 좋은것 아닌가?***

     *A. 맞다! 이때는 JPA에서 member를 조회할 때 항상 team을 같이 가지고 오도록 옵션을 킨다*

     

     ***반면 member를 가지고 오다가 아주 가끔씩 team을 가지고 올 때, 그때는 지연로딩이 더 효과적이다***

     

   - 즉시 로딩 : JOIN SQL로 한번에 연관된 객체까지 미리 조회

     ```java
     Member member = memberDao.find(memberId); // select M.*, T.*
     										  // from member
     										  // join team ...
     Team team = member.getTeam();
     String teamName = team.getName();
     ```

     