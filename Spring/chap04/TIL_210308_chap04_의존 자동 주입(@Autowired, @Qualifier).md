# 의존 자동 주입(@Autowired, @Qualifier)

설정 코드에서 의존 대상을 직접 주입

```java
@Configuration
public class AppCtx {

    @Bean // 해당 메서드가 생성한 객체를 스프링 빈이라고 설정
    public MemberDao memberDao(){
        return new MemberDao();
    }

    @Bean
    public ChangePasswordService changePasswordService(){
        ChangePasswordService changePasswordService
            = new ChangePasswordService();
        changePasswordService.setMemberDao(memberDao()); // 의존 주입
        return changePasswordService;
    }
}
```

-> 이렇게 의존 대상을 설정 코드에서 직접 주입하지 않고 스프링이 자동으로 의존하는 빈 객체를 주입해주는 기능도 있다. 

**자동 주입 설정 방법**

- @Autowired
- @Resource



## @Autowired

```java
@Bean
public MemberDao memberDao(){
    return new MemberDao();
}

@Bean
public ChangePasswordService changePasswordService(){
    ChangePasswordService changePasswordService = new ChangePasswordService();
    changePasswordService.setMemberDao(memberDao()); // 의존 주입
    return changePasswordService;
}

				↓ (자동 주입 기능을 사용하면 @Bean 메서드에서 의존을 
                ↓  주입하지 않아도 의존 객체가 주입된다)

@Bean
public MemberDao memberDao(){
    return new MemberDao();
}

@Bean
public ChangePasswordService changePasswordService(){
    ChangePasswordService changePasswordService = new ChangePasswordService();
    // changePasswordService.setMemberDao(memberDao());
    return changePasswordService;
}  
// 자동 주입 기능을 사용하면 의존 객체를 직접 명시하지 않아도 스프링이 의존 객체를 주입한다. 
```



**@Autowired 어노테이션을 적용한 대상에 일치하는 빈이 없다면?**

```java
@Configuration
public class AppCtx {

//    @Bean // 해당 메서드가 생성한 객체를 스프링 빈이라고 설정
//    public MemberDao memberDao(){
//       return new MemberDao();
//    }
    
    @Bean
    public MemberRegisterService memberRegSvc(){
        return new MemberRegisterService();
    }
}
```

-> Exception 발생하면서 제대로 실행되지 않는다.

Caused by: org.springframework.beans.factory.NoSuchBeanDefinitionException: No qualifying bean of type 'DI_example.MemberDao' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {@org.springframework.beans.factory.annotation.Autowired(required=true)}



**@Autowired 어노테이션을 붙인 주입 대상에 일치하는 빈이 두개 이상이면?**

```java
//@Bean // 해당 메서드가 생성한 객체를 스프링 빈이라고 설정
//public MemberDao memberDao(){
//    return new MemberDao();
//}
    
@Bean
public MemberPrinter memberPrinter1(){
    return new MemberPrinter();
}

@Bean
public MemberPrinter memberPrinter2(){
    return new MemberPrinter();
}
```

-> Exception in thread 'main' ...UnsatisfiedDependencyException : Error creating bean with ...

​    No qualifying bean of type 'spring.MemberPrinter' available: expected single matcing bean but found 2 

- 자동 주입을 하려면 해당 타입을 가진 빈이 어떤 빈인지 정확하게 한정할 수 있어야 하는데 MemberPrinter 타입의 빈이 두 개여서 어떤 빈을 자동 주입 대상으로 선택해야 할 지 한정할 수 없다.



### 자동 주입이 가능한 빈이 두 개 이상일 때, 자동 주입할 빈 지정하는 방법(@Qualifier)

@Qualifier 사용 위치

- @Bean 어노테이션을 붙인 빈 설정 메서드
- @Autowired 어노테이션에서 자동 주입할 빈을 한정할 때 사용

```java
//@Bean 어노테이션을 붙인 빈 설정 메서드
@Configuration
public class AppCtx {

    .... 
        
    @Bean
    @Qualifier("printer")
    public MemberPrinter memberPrinter1(){
        return new MemberPrinter();
    }

    @Bean
    public MemberPrinter memberPrinter2(){
        return new MemberPrinter();
    }
}
```

```java
public class MemberListPrinter {
    private MemberDao memberDao;
    private MemberPrinter memberPrinter;
    
    ...
        
    @Autowired
    @Qualifier("printer") // qualifier 값으로 printer를 준 memberPrinter1을 자동 주입 대상으로 사용
    public void setMemberPrinter(MemberPrinter memberPrinter) {
        this.memberPrinter = memberPrinter;
    }
}
```



*@Qualifier가 없으면 필드나 파라미터의 이름을 한정자로 사용한다.*



