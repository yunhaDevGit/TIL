# @Secured, @PreAutorize



```java
@EnableGlobalMethodSecurity(securedEnabled = true, prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    ...
}
```

**@EnableGlobalMethodSecurity**

- securedEnabled = true : @Secured 어노테이션 활성화
- prePostEnabled = true : @PreAuthorize, @PostAuthorize 어노테이션 활성화

![img](https://lh3.googleusercontent.com/YXpTqvxGWReVx3f_5hRkpYD9if2h588SEH9hz-u2oXw1kjil-8lEkDaz4FA7MicJ4D5j_VsTbDo7Z1k_8zA3FAuTq6l9l_MaYRCqK52hBrr4_VeM-puUlqkl_Rhv0nghSl6os9cq)

```java
@Controller
public class IndexController{
    
  @Secured("ROLE_ADMIN")
  @GetMapping("/info")
  public @ResponseBody String info(){
    return "개인정보";
  }

  @PreAuthorize("hasRole('ROLE_MANAGER') or hasRole('ROLE_ADMIN')")
  @GetMapping("/data")
  public @ResponseBody String data(){
    return "개인정보";
  }
}
```

@Secured("ROLE_ADMIN")

- 특정 메서드에 권한 설정

@PreAuthorize("hasRole('ROLE_MANAGER') or hasRole('ROLE_ADMIN')")

- 특정 메서드에 권한 설정
- data()라는 메서드가 실행되기 직전에 실행된다.
- @PreAuthorize("ROLE_ADMIN") -> 이런식으로 작성 X. hasRole(...)이렇게 작성해야 한다.