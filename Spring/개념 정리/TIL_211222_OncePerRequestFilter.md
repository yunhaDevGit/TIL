## addFilterBefore

```java
@EnableWebSecurity
public class BrmsWebSecurityConfiguration extends WebSecurityConfigurerAdapter {
    @Override
    public void configure(HttpSecurity http) throws Exception {
        http.addFilterBefore(new CustomAuthenticationProcessingFilter("/login-process"), 
                UsernamePasswordAuthenticationFilter.class);         
    }
} 
```

- Spring Security의 기본적인  filter 중 지정 된 filter 앞에 customfilter가 먼저 실행되도록 합니다. 


## OncePerRequestFilter

- 같은 요청에 대해 단 한 번만 처리가 수행되는 것을 보장하는 기반 클래스
