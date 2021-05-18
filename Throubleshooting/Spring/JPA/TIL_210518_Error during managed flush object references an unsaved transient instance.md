# Error during managed flush: object references an unsaved transient instance - save the transient instance before flushing



![img](https://lh6.googleusercontent.com/OV8I8GRA4cl2Ku6SQz2hYGcR8uf-rwI0vwo2oQY4SFcoQpSCrCPiiIlNgo9wckV0n-jt6l9s3V8TgAFPGYTIZl3TfGTNLUrmkIYWQwtWEKHS4Fjid3ry1-btqWY5Q5HcqZbfAydt)

Spring Data JPA를 사용하면서 @ManyToOne 어노테이션을 사용한 후 다음과 같은 에러메세지가 발생하였다.

'객체가 저장되지 않은 일시적인 인스턴스를 참조한다. 플러싱 하기 전에 임시 인스턴스를 저장해라'

Parent에 추가하는 Child 개체가 아직 데이터베이스에 저장되지 않아 생긴 문제!!

```java
public class JpaMain {
    public static void main(String[] args) {
        ...
        try {
            Team team = new Team();
            team.setName("teamA");

            Member member = new Member();
            member.setUsername("member");
            member.setTeam(team);
            entityManager.persist(member);

            Member findMember = entityManager.find(Member.class, member.getId());

            Team findTeam = findMember.getTeam();
            System.out.println("findTeam = " + findTeam.getName());

            entityTransaction.commit();
        }
        ...
    }
}
```

다음 코드를 아래와 같이 수정하니 제대로 동작했다

```java
public class JpaMain {
    public static void main(String[] args) {
		...
        try {
            Team team = new Team();
            team.setName("teamA");
            entityManager.persist(team); // 해당 줄 추가

            Member member = new Member();
            member.setUsername("member");
            member.setTeam(team);
            entityManager.persist(member);

            Member findMember = entityManager.find(Member.class, member.getId());

            Team findTeam = findMember.getTeam();
            System.out.println("findTeam = " + findTeam.getName());

            entityTransaction.commit();

        } 
        ...
    }
}
```

