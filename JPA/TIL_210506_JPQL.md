# JPQL

- 테이블이 아닌 **객체를 대상으로 한 객체지향 쿼리**

- JPA는 SQL을 추상화한 JPQL이라는 객체 지향 쿼리 언어를 제공한다. **객체지향 SQL**

- /META-INF/persistence.xml에 설정한 hibernate.dialect (방언)에 맞춰서 각 DB에 맞게 쿼리를 작성해준다

- JPA를 사용하면 엔티티 **객체를 중심으로 개발**한다. 문제는 검색 쿼리. (JOIN 같은 것...)

  검색을 할 때도, 테이블이 아닌 엔티티 객체를 대상으로 검색을 한다. (테이블에서 가지고 오게 되면 JPA의 사상이 깨지게 된다)

  테이블이 아닌 엔티티 객체를 대상으로 쿼리를 짤 수 있는 문법이 들어간다.

- 모든 DB 데이터를 객체로 변환해서 검색하는 것은 불가능하다

  결국, 애플리케이션이 필요한 데이터만 DB에서 불러오려면 결국엔 검색 조건이 포함된 sql이 필요하다.

  그걸 RDB(물리적인 테이블)을 대상으로 쿼리를 날리면, 데이터베이스에 종속적이게 된다.

  그게 아니라 **엔티티 객체를 대상으로 쿼리를 할 수 있는 JPQL을 제공**된다.

```java
public class JpaMain {
    public static void main(String[] args) {
        ...

        try{
            
            ...
            // JPQL
            // table을 대상으로 코드를 짜지 않는다. Member 객체를 대상으로 쿼리를 짠다. 여기서 Member는 객체이다
            List<Member> result = entityManager.createQuery("select m from Member as m", Member.class)
                    .getResultList();

            for(Member member : result){
                System.out.println("member.name = " + member.getName());
            }

            entityTransaction.commit();

        } catch( )
        ...

        entityManagerFactory.close();
    }
}
```

![img](https://lh5.googleusercontent.com/L_7QHimd6HMfP1vL2DXJpgdWYFhCQnP9bV-0AUp3-uoxGRlUh-hbsDFdF8UGE5ZL3genEPSqD5RzuXm-xdo_N9tvfNd-fwMR3Z0up6uqQypbQfZXuNKqLRdpCB3ifwiFw8kmC2O2)

- 내가 작성한 JPQL과 실행된 sql문이 다르다. 



----

JPQL

- **엔티티 객체를 대상으로 쿼리**



SQL

- **데이터베이스 테이블을 대상으로 쿼리**