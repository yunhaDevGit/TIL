# DI 방식 두 가지



### 생성자를 통한 의존성 주입

```java
public class MemberRegisterService {
    
    private MemeberDao memberDao;
    // 생성자를 통해 의존 객체 주입
    public MemberRegisterService(MemberDao memberDao){
        // 주입 받은 의존 객체의 메서드 사용
        this.memberDao = memberDao;
    }
}
```

```java
@Bean
public MemberDao memberDao(){
    return new memberDao;
}

@Bean
pubic MemberRegisterService memberRegisterService(){
    return new MemberRegisterService(memberDao());
}
```



### Setter 메서드 방식

생성자 외에 세터 메서드를 이용해서 객체를 주입받기도 한다.

- 메서드 이름이 set으로 시작
- set 뒤에 첫 글자는 대문자로 시작
- 파라미터가 1개이다
- 리턴 타입이 void이다

```java
public class MembeInforPrinter{
    
    private MemberDao memberDao;
    private MemberPrinter memberPrinter;
    
    public void setMemberDao(MemberDao memberDao){
        this.memberDao = memberDao;
    }
    
    public void setMemeberPrinter memberPrinter(MemberPrinter memberPrinter){
        this.memPrinter = memberPrinter;
    }
}
```



생성자 방식

- 빈 객체를 생성하는 시점에 모든 의존 객체가 주입된다.
- 생성자 파라미터 개수가 많을 경우, 각 인자가 어떤 의존 객체를 설정하는지 알아내려면 생성자 코드를 확인해야 한다. 
- 빈 객채를 생성하는 시점에 필요한 모든 의존 객체를 주입받기 때문에 객체를 사용할 때 완전한 상태로 사용할 수 있다. 



설정 메서드 방식

- 세터 메서드 이름을 통해 의존 객체가 주입되는지 알 수 있다.
- 메서드 이름만으로도 어떤 의존 객체를 설정하는지 쉽게 유추할 수 있다. 
- 세터 메서드를 사용해서 필요한 의존 객체를 전달하지 않아도 빈 객체가 생성되기 때문에 객체를 사용하는 시점에 NullPointerException이 발생할 수 있다.

