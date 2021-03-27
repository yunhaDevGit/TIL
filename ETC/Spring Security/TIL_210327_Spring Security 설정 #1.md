# Spring Security 설정 #1

```java
@Configuration
@EnableWebSecurity 
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    
  @Override
  protected void configure(HttpSecurity http) throws Exception {
    http.csrf().disable();
    http.authorizeRequests()
      .antMatchers("/user/**").authenticated() 
      .antMatchers("/manager/**").access("hasRole('ROLE_ADMIN') or hasRole('ROLE_MANAGER')") 			
      .antMatchers("/admin/**").access("hasRole('ROLE_ADMIN')")
      .anyRequest().permitAll();
  }
}
```

**@Configuration** : spring의 환경 설정과 관련된 파일이라는 것을 알림. 메모리에 올려준다.



**@EnableWebSecurity**  (스프링 시큐리티 사용을 위한 어노테이션)

- springSecurityFilterChain이 스프링 필터체인에 자동으로 등록된다

  (=SecurityConfig(내가 지금부터 등록할 필터)가 기본 필터 체인에 등록된다)

  

**WebSecurityConfigureAdapter**

- 사용자 지정 자체 보안 구성을 원할 때 상속받아 사용하는 클래스
  - 해당 클래스를 상속 받아 구현하게 되면 기본 자동 구성이 비활성화되고, 사용자 지정 보안 구성이 활성화된다.
  - @EnableWebSecurity와 웹 기반 보안을 위해 같이 사용된다.
- WebSecurity와 HttpSecurity를 사용자가 원하는대로 지정할 수 있도록 해준다



**configure(WebSecurity) / configure(HttpSecurity)**

- configure(WebSecurity)
  - 특정 요청들을 무시하고 싶을 때 사용
- configure(HttpSecurity)
  - 특정 http 요청에 대해 웹 기반 보안을 구성할 수 있다



**.antMatchers()로 지정할 수 있는 항목**

.authenticated()  : 인증된 사용자에게만 접근 허용

.access("hasRole('ROLE_ADMIN')") : 특정 권한을 가지는 사용자에게만 접근 허용

.permitAll()  : 모든 요청에 대해 접근 허용

.denyAll() : 모든 요청에 대해 접근 제한

.anonymous() : 인증되지 않은 사용자에게 접근 허용

.hasIpAddress() : 특정 ip 주소를 가지는 사용자에게만 접근 허용



------

```java
.antMatchers("/admin/**").access("hasRole('ROLE_ADMIN')")
```

-> 'ROLE_ADMIN'이 아닌 사용자가 접근 요청을 했을 경우 403 접근 권한 에러 발생

<img src="https://lh4.googleusercontent.com/86iUJr_I0Dhi5TvpGHIs-YSOdE6lHLKHwcf6uC9DZETlGonR-_aYWWS-fjpT4L-8JSxpjJsrWh5ZNgfPz9jRekBdY5ZW9ugtTyqfEbnDuPukjTS2GXHb8vkmXev36fDK36XGOWHg" alt="img" style="zoom:80%;" />

