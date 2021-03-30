# 구글 로그인 후 처리(회원 프로필 정보 받아오기)

#### SecurityConfig.java

```java
@Configuration
@EnableWebSecurity 
@EnableGlobalMethodSecurity(securedEnabled = true, prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {

  @Autowired
  public PrincipleOauth2UserService principleOauth2UserService;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
      http.csrf().disable();
      http.authorizeRequests()
        ...
        .anyRequest().permitAll() 
        .and()
        .formLogin()
        .loginPage("/loginForm") 
        ...
        .and()
        .oauth2Login()
        .loginPage("/loginForm") 
        .userInfoEndpoint()
        .userService(principleOauth2UserService);
  }
}
```



**oauth2 로그인 완료 후 후처리**

.userInfoEndPoint() -> OAuth2 로그인 성공 이후 사용자 정보를 가져올 때의 설정 담당

.userService() -> 소셜 로그인 성공 시 후 처리를 진행할 UserService 인터페이스의 구현체를 등록

(리소스 서버에서 사용자 정보를 가져온 상태에서 추가로 진행하고자 하는 기능을 명시할 수 있다)



> **구글 로그인**
>
> 1. 코드 받는다(인증 : 구글에 정상적으로 로그인한 사용자임을 인증 받음) 
>
> 2. 코드를 통해 엑세스 토큰을 받는다(권한) 
>
> 3. 사용자 프로필 정보를 받아오기(사용자 정보에 접근할 수 있는 권한)
>
> 4. 1) 사용자 정보를 토대로 회원가입을 자동으로 진행시키기도 함
>
>    2) (이메일, 전화번호, 이름, 아이디) 외의 추가적인 정보가 필요하다면 추가 정보 입력 후 회원 가입



**Tip. 구글 로그인이 완료**

- 엑세스 토큰 + 사용자 프로필 정보를 모두 받는다



#### PrincipalOauth2UserService.java

```java
@Service
public class PrincipleOauth2UserService extends DefaultOAuth2UserService {

  @Override
  public OAuth2User loadUser(OAuth2UserRequest userRequest) throws OAuth2AuthenticationException {
    System.out.println("getClientRegistration : "+userRequest.getClientRegistration());
    System.out.println("getAccessToken : "+userRequest.getAccessToken());
    System.out.println("getAttributes : "+ super.loadUser(userRequest).getAttributes());
    
    return super.loadUser(userRequest);
  }
}
```

spring security 설정 클래스에서  **.userService(principleOauth2UserService)** 설정을 해주게 되면principleOauth2UserService의 **loadUser**() 라는 함수에서 모든 **후 처리**가 수행된다. 

(-> 구글로부터 받은 userRequest 데이터에 대한 후 처리를 해준다)



**getClientRegistration** : ClientRegistration{registrationId='google', ...} 

- registrationId로 어떤 OAuth로 로그인했는지 확인할 수 있다.

**getAccessToken** : org.springframework.security.oauth2.core.OAuth2AccessToken@e25e5a98 

**getAttributes** : {sub=104227030904767590535,  name=yunha,  given_name=yunha,  picture=https://~.jpg, [email=yunhadev98@gmail.com](mailto:email=yunhadev98@gmail.com),  email_verified=true,  locale=ko} 



> registration, token 정보가 userRequest 안에 있다



**UserRequest 정보**

구글 로그인 버튼 클릭 -> 구글 로그인창 -> 로그인 완료 -> code를 리턴(OAuth-Client 라이브러리가 받는다) -> code를 통해 Access Token 요청  -> Access Token 리턴   `▶ UserRequest 정보`

UserRequest 정보 -> loadUser() 메서드 호출 -> 구글로부터 회원 프로필 정보 받는다



> username = 'google_104227030904767590535'
>
> password = '암호화(갯인데어)'
>
> email = yunhadev98@gmail.com
>
> role = 'ROLE_USER'
>
> provider = 'google'
>
> providerId = 104227030904767590535

