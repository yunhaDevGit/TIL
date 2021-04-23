# JWT를 위한 Security 설정

```java
@Configuration
@EnableWebSecurity
@RequiredArgsConstructor //
public class SecurityConfig extends WebSecurityConfigurerAdapter {

  private final CorsFilter corsFilter;
  private final UserRepository userRepository;

  @Override
  protected void configure(HttpSecurity http) throws Exception {

    http.csrf().disable();

    // 세션을 사용하지 않겠다
    // -> stateless 서버로 만들겠다
    http.sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
        .and()
        .addFilter(corsFilter) // 인증 있을 때 security filter 등록!!
        .formLogin().disable() // formLogin 안쓰겠다 (jwt 방식으로 로그인 하기 때문에 기존에 id, pw 쓸 필요 x
        .httpBasic().disable() // 기존의 하던 방식과는 다른 방식
        .addFilter(new JwtAuthenticationFilter(authenticationManager()))
        .addFilter(new JwtAuthorizationFilter(authenticationManager(), userRepository))
        .authorizeRequests()
        .antMatchers("/api/v1/user/**")
        .access("hasRole('ROLE_USER') or hasRole('ROLE_MANAGER') or hasRole('ROLE_ADMIN')")
        .antMatchers("/api/v1/manager/**")
        .access("hasRole('ROLE_MANAGER') or hasRole('ROLE_ADMIN')")
        .antMatchers("/api/v1/admin/**")
        .access("hasRole('ROLE_ADMIN')")
        .anyRequest().permitAll(); // 다른 요청은 전부 허용(권한 없이 접근 가능)
  }
}
```

```java
@Configuration
public class CorsConfig {

  @Bean
  public CorsFilter corsFilter() {

    UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
    CorsConfiguration config = new CorsConfiguration();
    config.setAllowCredentials(true); // 내 서버가 응답을 할 때 json을 자바스크립트에서 처리할 수 있게 할지를 설정
    // false -> 자바스크립트로 어떤 요청을 했을때 응답이 오지 않는다
    config.addAllowedOrigin("*"); // 모든 ip의 응답을 허용
    config.addAllowedHeader("*");; // 모든 header의 응답을 허용
    config.addAllowedMethod("*"); // 모든 post, get, put, delete 요청을 허용
    source.registerCorsConfiguration("/api/**", config); // /api/**로 들어오는 모든 주소는 이 config를 따른다
    return new CorsFilter(source);
  }
}
```



**JWT 사용하여 로그인 처리 해야 할 때, 기본으로 작성해줘야 하는 것!!!**

- http.csrf().disable()
-   http.sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
          .and()
          .addFilter(corsFilter) 
          .formLogin().disable() 
          .httpBasic().disable() 





**세션 사용하는 방식**

클라이언트 -> id, pw(최초 요청) -> 서버 - 세션>세션 ID 만든다

id, pw 정상 -> 세션 ID에 user object 저장 후 세션 ID 리턴 -> 프로그램 쿠키 영역 : 세션 ID 저장 

새로운 요청 -> cookie의 세션 ID를 들고 간다 -> 들고온 세션 ID를 통해 인증 처리



**이 방식의 단점**

- 서버가 하나일 때는 괜찮은데 서버가 여러개일 때는 안좋다. 
  
  - 서버마다 세션 영역이 따로 있기 때문에 서버가 많아질 수록 확장성이 떨어진다
  
- fetch('http://www.naver.com', {

  ​	headers : {

  ​		Cookie: ""

  ​	}

  })

  이런식으로 javascript에서 직접 cookie를 넣어 줄 수 있다. 보통은 이런식으로 오는 요청은 서버 쪽에서 거부를 하는데, http only = false로 걸어버리면 요청이 오긴 한다. 

  만약 이렇게 풀어주면 보안적인 관점에서 좋지 않다.





header

​	Basic 방식 => Authorization : ID, PW  -> 노출 위험 있다. 

​	Bearer 방식 => Authorization : token  -> 노출 위험 있다. 하지만 자체가 id, pw는 아니기 때문에 위험은 비교적 적다

>Basic 방식은 id, pw가 노출되면 해당 id, pw를 변경하기 전까지는 계속 뚫린다
>
>Bearer 방식은 token이 노출되면 안되는 것은 마찬가지지만 노출되더라도 token은 id, pw로 로그인 할 때마다 계속 다시 만들어주기 때문에 비교적 안전하다. (token은 유효시간이 있다.)



