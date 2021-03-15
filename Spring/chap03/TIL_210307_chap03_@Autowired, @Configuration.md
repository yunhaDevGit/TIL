# @Autowired, @Configuration

스프링 빈에 의존하는 다른 빈을 자동으로 주입하고 싶을 때 사용.

**example**

```java
public class MemberInfoPrinter{
    
    @Autowired
    private MemberDao memberDao;
    @Autowired
    private MemberPrinter memberPrinter;
    
    public void printMemberInfo(String email){
        Member member = memberDao.selectByEmail(email);
        if(member==null)
            System.out.printf("데이터 없음\n");
            return;
        }
        memberPrinter.print(member);
        System.out.println();
    }

    public void setMemberDao(MemberDao memberDao){
        this.memberDao = memberDao;
    }

    public void setMemberPrinter(MemberPrinter memberPrinter){
        this.memberPrinter = memberPrinter;
    }
}
```

memberDao와 memberPrinter 두 필드에 @Autowired 어노테이션을 붙이면 스프링 설정 클래스의 @Bean 메서드에서 의존 주입을 위한 코드를 작성하지 않아도 된다.



#### @Autowired 안붙였을 경우

```java
@Bean
public MemberInfoPrinter memberInfoPrinter(){
    MemberInfoPrinter memberInfoPrinter = new MemberInfoPrinter();
    memberInfoPrinter.setMemberDao(memberDao);
    memberInfoPrinter.setMemberPrinter(memberPrinter);
    return memberInfoPrinter;
}
```



#### @Autowired 붙였을 경우

```java
@Bean
public MemberInfoPrinter memberInfoPrinter(){
    MemberInfoPrinter memberInfoPrinter = new MemberInfoPrinter();
    // Setter 메서드를 사용해서 의존 주입을 하지 않아도
    // 스프링 컨테이너가 @Autowired를 붙인 필드에 자동으로 해당 타입의 빈 객체를 주입
    return memberInfoPrinter;
}
```



스프링 컨테이너는 설정 클래스에서 사용한 @Autowired에 대해서도 자동 주입을 처리한다. 

**@Configuration 어노테이션이 붙은 설정 클래스를 내부적으로 스프링 빈으로 등록한다. **

그리고 다른 빈과 마찬가지로 **@Autowired**가 붙은 대상에 대해 알맞은 빈을 자동으로 주입한다. 

```java
@Configuration // 해당 어노테이션이 붙은 클래스를 내부적으로 스프링 빈으로 등록한다.
public class AppConf2{
    @Autowired
    private MemberDao memberDao;
    @Autowired
    private MemberPrinter memberPrinter;
}
```

-> 스프링 컨테이너는 AppConf2 객체를 빈으로 등록

-> @Autowired가 붙은 대상에 대해 알맞은 빈을 자동 주입



``` java
AbstractApplicationContext ctx 
    = new AnnotationConfigApplicationContext(AppConf1.class, AppConf2.class);

// @Configuration 설정 클래스도 빈으로 등록한다.
AppConf1 appConf1 = ctx.getBean(AppConf1.class);
```

