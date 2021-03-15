# @Autowired(required=false), Optional, @Nullable



```java
public class MemberPrinter {

    private DateTimeFormatter dateTimeFormatter;
    
    public void print(Member member){
        if(dateTimeFormatter==null) {
            System.out.printf("회원 정보 : 아이디=%d, 이메일=%s, 이름=%s, 등록일=%tF\n",
                    member.getId(), member.getEmail(), member.getName(), member.getRegisterDateTime());
        } else {
            System.out.printf("회원 정보 : 아이디=%d, 이메일=%s, 이름=%s, 등록일=%tF\n",
                    member.getId(), member.getEmail(), member.getName(),
                              dateTimeFormatter.format(member.getRegisterDateTime()));
        }
    }
    
    @Autowired
    public void setDateFormatter(DateTimeFormatter dateTimeFormatter){
        this.dateTimeFormatter = dateTimeFormatter;
    }
}
```

print() 메서드는 dateTimeFormatter가 null인 경우에도 알맞게 동작한다. 

-> 반드시 setDateFormatter()를 통해 의존 객체를 주입할 필요가 없다.



하지만, @Autowired 어노테이션은 **@Autowired를 붙인 타입에 해당하는 빈이 존재하지 않으면 익셉션이 발생한다**

-> setDateFormatter()에서 필요로 하는 DateTimeFormatter 타입의 빈이 존재하지 않으면 익셉션이 발생한다. 

-> MemberPrinter는 setDateFormatter()에 자동 주입할 빈이 존재하지 않으면 익셉션이 발생하기 보다는 dateTimeFormatter가 null이면 된다.



### 의존성 주입 필수 여부 지정 방법 세가지

***방법 1.이렇게 자동 주입할 대상이 필수가 아닌 경우에는 @Autowired 어노테이션에 required 속성을 false로 지정해주면 된다.***

```java
@Autowired(required=false)
public void setDateFormatter(DateTimeFormatter dateTimeFormatter){
   this.dateTimeFormatter = dateTimeFormatter;
}
```

- required 속성을 false로 지정하면 **매칭되는 빈이 없어도 익셉션이 발생하지 않고, 자동 주입을 수행하지 않는다. **
- 위의 예에서 DateTimeFormatter 타입의 빈이 없으면 익셉션을 발생하지 않고 setDateFormatter 메서드를 실행하지 않는다.



***방법 2. 스프링 5부터 @Autowired(required=false) 대신 자바 8의 Optional을 사용해도 된다***

```java
@Autowired
public void setDateFormatter(Optional<DateTimeFormatter> formatterOpt){
        // 값 존재 여부에 따라 값을 dateTimeFormatter에 할당
    if(formatterOpt.isPresent())
        this.dateTimeFormatter = formatterOpt.get();
    else
        this.dateTimeFormatter = null;
}
```

*Optional : 존재할 수도 있지만 안 할 수도 있다. null이 될 수도 있는 객체*

- 자동 주입 대상 타입이 Optional일 경우, 일치하는 빈이 존재하지 않으면 값이 없는 Optional을 인자로 전달하고 익셉션을 발생 x
- 일치하는 빈이 존재하면 해당 빈을 값으로 갖는 Optional을 인자로 전달



***방법3. @Nullable 어노테이션을 사용한다.***

```java
@Autowired
public void setDateFormatter(@Nullable DateTimeFormatter dateTimeFormatter){
    this.dateTimeFormatter = dateTimeFormatter;
}
```

@Autowired 어노테이션을 붙인 세터 메서드에 @Nullable 어노테이션을 의존성 주입 대상 파라미터에 붙이면,  스프링 컨테이너는 세터 메서드를 호출할 때 **주입할 빈이 존재하면 해당 빈을 인자로 전달**하고, **존재하지 않으면 인자로 null을 전달**한다.



:heavy_plus_sign: @Autowired의 required=false 속성과 @Nullable의 차이점

​	@Nullable은 자동 주입할 빈이 존재하지 않아도 메서드가 호출된다.

​	반면, @Autowired의 required = false 속성을 적용하면, 대상 빈이 존재하지 않을 경우, 세터 메서드를 호출하지 않는다.



앞선 세가지 방식 모두 메서드에 한 것처럼 필드에도 그대로 적용된다.

```java
public class MemberPrinter{
    @Autowired(required=false)
    private DateTimeFormatter dateTimeFormatter;
    
    ...
}
```

```java
public class MemberPrinter{
    @Autowired
    private Optional <DateTimeFormatter> dateTimeFormatter;
    
    ...
}
```

```java
public class MemberPrinter{
    @Autowired
    @Nullable
    private DateTimeFormatter dateTimeFormatter;
    
    ...
}
```

