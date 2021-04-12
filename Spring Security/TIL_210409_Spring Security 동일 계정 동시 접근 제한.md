### Spring Security 동일 계정 동시 접근 제한

```java
@Bean
public SessionRegistry sessionRegistry() {
	return new SessionRegistryImpl();
}

@Override
protected void configure(HttpSecurity http) throws Exception {
	...
        
    http
        .sessionManagement()
        .sessionFixation()
        .newSession() 
        .maximumSessions(1)   
        .sessionRegistry(sessionRegistry()) 
        .maxSessionsPreventsLogin(false) 
        .expiredUrl("/login?error=sessionExpired");  
    
    ...
}
```

**.sessionManagement()**

- 세션 관리 기능 작동

**.sessionFixation()**

- 악의적인 사용자가 사이트에 접근하기 위한 세션을 만들고, 그 세션을 통해 다른 사용자로 로그인 하려고 하는 경우 위험하다.

  스프링 시큐리티가 그 공격을 자동으로 막기 위해 사용자 로그인을 할 때마다 새로운 세션을 생성한다.

  그 때 sessionFixation으로 설정할 수 있다.

  - changeSessionId()
    - 새로운 세션 ID 발급
    - 이전 세션 사용 가능
  - newSession() 
    - 새로운 세션 ID 발급
    - 이전 세션 사용 불가
  - none()
    - 아무 설정 하지 않는다

  > .sessionManagement()
  >
  > .sessionFixation()
  >
  > .changeSessionId();

**.newSession()**

- 새로운 세션 ID 발급, 이전 세션 사용 불가(기존 세션 데이터를 복사하지 않는다)

**.maximumSessions(1)**

- 세션 허용 개수, 같은 아이디로 1명만 로그인 할 수 있다.

**.sessionRegistry(sessionRegistry())**

- 해당 설정을 추가해주지 않으면 logout 후 다시 로그인 할 때 "Maximum sessions of 1 for this principal exceeded"  에러를 발생시키며 로그인 되지 않는다. **필수!!**

**.maxSessionsPreventsLogin(false)**

- 신규 로그인 사용자의 로그인이 허용되고, 기존 사용자는 세션 아웃 된다.

**.expireUrl()**

- 중복 로그인이 일어났을 경우 이동할 주소