# getBean() 메서드

지금껏 getBean() 메서드를 이용해 사용할 빈 객체를 구했다.

```java
VersionPrinter versionPrinter
    = new ctx.getBean("versionPrinter", VersionPrinter.class);
```

getBean() 메서드

- **첫 번째 인자 : 빈 이름**
- **두 번째 인자 : 빈 타입**



첫 번째 인자에 **존재하지 않는 빈의 이름**을 넣었을 경우

-> Exception in thread "main" org.springframeword.beans.factory.NoSuchDefinitionException;

​     No bean named 'versionPrinter2' available



두 번째 인자에 **지정한 타입과 실제 빈의 타입이 다를 경우**

-> Exception in thread "main" org.springframeword.beans.factory.BeanNotOfRequredTypeException;

​    Bean named '빈 이름' is expected to be of type 'spring.작성된 타입' but was actually of type 'spring.실제 타입'

​     

#### 빈 이름을 지정하지 않고, 타입만으로 빈 구하기

```java
VersionPrinter versionPrinter = ctx.getBean(MemberPrinter.class);
```

해당 타입의 빈 객체가 **한개만 존재하면 해당 빈을 구해서 리턴.**



만약 해당 타입의 빈 객체가 **존재하지 않으면 exception 발생**

-> Exception in thread "main" org.springframeword.beans.factory.NoSuchDefinitionException; 

​    No qualifying bean of type 'spring.MemberPrinter' available



같은 타입의 빈 객체가 **두 개 이상 존재할 경우**

```java
@Configuration
public class AppCtx {
    ...
    @Bean
    public VersionPrinter versionPrinter(){
        ...
        return versionPrinter;
    }
    
    @Bean
    public VersionPrinter oldVersionPrinter(){
        ...
        return versionPrinter;
    }
    
}
```

-> 같은 타입의 bean 이 두 개 있을 경우 ctx.getBean(VersionPrinter.class) 실행 시키면 exception 발생

-> Exception in thread "main" org.springframeword.beans.factory.NoUniqueBeanDefinitionException; 

​    No qualifying bean of type 'spring.VersionPrinter' available : expected single matching bean but found 2: versionPrinter, oldVersionPrinter





*getBean() 메서드는 BeanFactory 인터페이스에 정의되어 있다. getBean() 메서드를 실제로 구현한 클래스는 AbstractApplicationContext이다.*



------

*주입할 객체가 꼭 스프링 빈일 필요는 없다. 일반 객체로 생성해서 주입해도 된다.*

```java
@Configuration
public class AppCtxNoMemberPrinterBean{
    private MemberPrinter memberPrinter = new MemberPrinter(); // 빈이 아니다.
    ...
    
    @Bean
    public MemberListPrinter listPrinter(){
        return new MemberListPrinter(memberDao(), memberPrinter);
    }
}
```

MemberPrinter를 빈으로 등록하지 않아도, 해당 객체를 memberPrinter 필드에 할당 후 사용해도 MemberListPrinter 객체는 정상적으로 동작한다.



객체를 스프링 빈으로 등록할 때와 등록하지 않을 때의 차이는 **스프링 컨테이너가 객체를 관리하는지 여부**이다.

```java
// MemberPrinter를 빈으로 등록하지 않았으므로 아래 코드는 exception 발생
MemberPrinter memberPrinter = ctx.getBean(MemberPrinter.class);
```



*스프링 컨테이너는 빈으로 등록된 객체에 대해서만 자동 주입, 라이프 사이클 관리 등의 객체 생성 및 관리를 위한 기능을 제공한다.*

