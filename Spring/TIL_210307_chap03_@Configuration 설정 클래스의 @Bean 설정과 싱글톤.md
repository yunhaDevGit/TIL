# @Configuration 설정 클래스의 @Bean 설정과 싱글톤

```java
@Configuration
public class AppCtx {

    @Bean
    public MemberDao memberDao(){
        return new MemberDao();
    }

    @Bean
    public MemberRegisterService memberRegisterService(){
        return new MemberRegisterService(memberDao());
    }

    @Bean
    public ChangePasswordService changePasswordService(){
        ChangePasswordService changePasswordService 
            = new ChangePasswordService();
        changePasswordService.setMemberDao(memberDao());
        return changePasswordService;
    }
}
```

memberRegisterService()와 changePasswordService() 메서드 모두 memberDao() 메서드를 실행하고, memberDao 메서드는 항상 새로운 MemberDao 객체를 생성하여 리턴한다.

:question: memberDao() 가 새로운 MemberDao 객체를 생성하여 리턴하니까 memberRegisterService()에서 생성한 MemberRegisterService 객체와 changePasswordService()의 ChangePasswordService 객체는 서로 다른 memberDao 객체를 사용하나?

:arrow_forward: 스프링 컨테이너가 생성한 빈은 싱글톤 객체이다. 스프링 컨테이너는 @Bean이 붙은 메서드에 대해 한 개의 객체만 생성한다. **다른 설정 메서드에서 memberDao() 객체를 몇 번을 호출하더라도 항상 같은 객체를 리턴한다는 것을 의미한다**



:question: 어떻게 가능하지?

:arrow_forward: 스프링은 설정 클래스를 그대로 사용하지 않는다.  대신 설정 클래스를 상속한 새로운 설정 클래스를 만들어서 사용한다. 스프링이 런타임에 생성한 설정 클래스는 아래와 비슷한 방식으로 동작하다. (그냥 예시일 뿐 실제 스프링 코드는 훨씬 더 복잡하다. 그냥 이런식으로 동작한다구ㅎㅎ)

```java
public class AppCtxExt extends AppCtx {
    private Map<String, Object> beans = ...;
    
    @Override
    public MemberDao memberDao(){
        if(!beans.containsKey("memberDao"))
            beans.put("memberDao", super.memberDao());
        
        return (MemberDao) beans.get("memberDao");
    }
}
```

-> 스프링이 런타임에 생성한 설정 클래스의 memberDao() 메서드는 매번 새로운 객체를 생성하는 것이 아닌, 한 번 생성한 객체를 보관하다가 이후에 동일한 객체를 리턴한다.

**memberRegisterService()와 changePasswordService() 메서드에서 memberDao() 메서드는 각각 실행해도 동일한 memberDao 객체를 사용한다.**



*스프링이 관리하는 오브젝트인 Bean의 scope는 기본적으로 싱글톤 scope이다.*



*왜 싱글톤패턴을 사용할까?*

- 만약 우리가 요청을 할 때마다 새로운 객체를 생성할 경우, 요청이 엄청나게 많은 트래픽 사이트에서는 객체를 계속 생성하면 **메모리 낭비가 심하기 때문이다.**

