# Spring Security 프로젝트에 Swagger 적용

Spring Security가 적용된 프로젝트에서 Swagger를 적용할 경우,
swagger-ui와 /v3/api-docs에 접근하기 위해 아래와 같이 설정해줘야 한다.

```java
@Slf4j
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(securedEnabled = true)
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    public void configure(WebSecurity web) throws Exception {
        log.info("configure: {}", web);
        web
                .ignoring()
                .antMatchers("/swagger-ui/**",  "/v3/api-docs/**", "/v3/api-docs");
    }
    
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        log.info("configure: {}", http);
        http
                .cors().and().csrf().disable()
                .antMatchers("/swagger-ui/**",  "/v3/api-docs/**", "/v3/api-docs").permitAll()
                .anyRequest().authenticated();
    }
    ...
}   
```
