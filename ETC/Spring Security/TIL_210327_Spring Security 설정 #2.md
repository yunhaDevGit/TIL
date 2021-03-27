# Spring Security 설정 #2

```java
@Configuration
@EnableWebSecurity 
@EnableGlobalMethodSecurity(securedEnabled = true, prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    
  @Override
  protected void configure(HttpSecurity http) throws Exception {
    http.csrf().disable();
    http.authorizeRequests()
      .antMatchers("/user/**").authenticated() 
      .antMatchers("/manager/**").access("hasRole('ROLE_ADMIN') or hasRole('ROLE_MANAGER')") 			
      .antMatchers("/admin/**").access("hasRole('ROLE_ADMIN')")
      .anyRequest().permitAll() 
      .and()
      .formLogin()
      .loginPage("/loginForm") 
      .loginProcessingUrl("/login") 
      .defaultSuccessUrl("/");
  }
}
```



.formLogin().loginPage("/loginForm")  

- form을 통한 login 활성화. login 페이지에 접근했을 때 사용자에게 보여줄 페이지 url 지정
- 권한이 없는 페이지로 요청이 들어갈 때, 로그인 페이지로 이동된다

.loginProcessingUrl("/login") 

- login 주소가 호출되면 시큐리티가 낚아채서 대신 로그인을 진행해준다.(controller에 /login을 만들지 않아도 된다)

- 로그인 form의 action과 일치시켜 줘야 한다.

  <img src="https://lh3.googleusercontent.com/VwiSyNuZQGI6sOIDojXN5RMiz9-SFNAIiWJoDl3BaYikDOUR5_C91wQf3M-dk1QuTaPXFNIeFmlwGnuz-wHsibALRZMjHnKD8sxH5ACCFVxHyjxbsOW-FzvQ9He_19kMndy5f4fy" alt="img" style="zoom:80%;" />

.defaultSuccessUrl("/") 

- 로그인 성공 시 '/' url 요청



**@EnableGlobalMethodSecurity**

- securedEnabled = true : @Secured 어노테이션 활성화
- prePostEnabled = true : @PreAuthorize, @PostAuthorize 어노테이션 활성화

