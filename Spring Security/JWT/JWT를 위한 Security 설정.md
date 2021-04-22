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



클라이언트 -> id, pw(최초 요청) -> 서버 - 세션>세션 ID 만든다

id, pw 정상 -> 세션 ID에 user object 저장 후 세션 ID 리턴 -> 프로그램 쿠키 영역 : 세션 ID 저장 

새로운 요청 -> cookie의 세션 ID를 들고 간다 -> 들고온 세션 ID를 통해 인증 처리



**이 방식의 단점**

- 서버가 하나일 때는 괜찮은데 서버가 여러개일 때는 안좋다. 
  - 서버마다 세션 영역이 따로 있기 때문에 서버가 많아질 수록 확장성이 떨어진다
- 

