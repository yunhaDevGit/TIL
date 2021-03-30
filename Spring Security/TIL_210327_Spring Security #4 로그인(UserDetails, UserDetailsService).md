# Security Session, Authentication, UserDetails, UserDetailsService

```java
@Configuration
@EnableWebSecurity 
@EnableGlobalMethodSecurity(securedEnabled = true, prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {

 
    @Override
    protected void configure(HttpSecurity http) throws Exception {
      http.csrf().disable();
      http.authorizeRequests()
        ...
        .loginProcessingUrl("/login") //login 주소가 호출이 되면 시큐리티가 낚아채서 대신 로그인을 진행해준다
        .defaultSuccessUrl("/")
        ...
  }
}

```



시큐리티가 /login 주소 요청이 오면 낚아채서 실제 로그인을 진행시킨다.



*로그인 진행이 완료가 되면 session에 넣어주게 된다. 일반적인 session과 세션 공간은 똑같은데 security가 자신만의 security session 공간을 가진다. key 값으로 구분(key값:SecurityContextHolder)*



Session에 들어갈 수 있는 오브젝트가 정해져있다.

- Authentication 타입의 객체여야 한다.
- Authentication 객체 안에 User 정보가 있어야 한다.
  - User 객체의 타입도 정해져있다 : UserDetails 타입 객체

> Security Session 영역에 세션 정보 저장
>
> Security Session => Authentication => UserDetails
>
> 즉, **Security Session** 영역에 세션 정보를 저장해주는데, 들어갈 수 있는 객체는 **Authentication** 객체이다. Authentication 객체 안에 user 정보를 저장하는데 User정보를 담는 객체는 **UserDetails** 타입이다.





---

**접근 주체(principal)** : 보호된 대상에 접근하는 클라이언트

**인증(Authentication)** : 현재 유저가 누군지 확인 / 어플리케이션의 작업을 수행할 수 있는 주체임을 증명하는 과정

**인가(Authorize)** : 현재 유저가 어떤 서비스, 페이지에 접근할 수 있는 권한이 있는지 검사

**권한(Authorization)** : 인증된 주체가 어플리케이션의 동작을 수행할 수 있도록 허락되있는지를 결정

---



#### UserDetails

- security애서 사용자의 정보를 담는 인터페이스
- user 정보를 가져와 담는 VO 역할로 사용
- UserDetails 인터페이스 구현 시 오버라이드 되는 메서드
  - getAuthorities() : 계정이 가지고 있는 권한 목록 리턴
  - getPassword() : 계정의 비밀번호 리턴
  - getUsername() : 계정의 이름 리턴
  - isAccountNonExpired() : 계정이 만료되지 않았는지 리턴(true : 만료 안됨)
  - isAccountNonLocked() : 계정이 잠겨있지 않았는지 리턴(true : 잠기지 않음)
  - isCredentialNonExpried() : 비밀번호가 만료되지 않았는지 리턴(true : 만료 안됨)
  - isEnabled() : 계정이 활성화인 지 리턴(true : 활성화)

```java
@Data
public class PrincipalDetails implements UserDetails {

  private User user;
  private Map<String,Object> attributes;

  public PrincipalDetails(User user){
    this.user = user;
  }

  public PrincipalDetails(User user, Map<String,Object> attributes){
    this.user = user;
    this.attributes = attributes;
  }

  @Override
  public Collection<? extends GrantedAuthority> getAuthorities() {
    Collection<GrantedAuthority> collect = new ArrayList<>();
    collect.add(new GrantedAuthority() {
      @Override
      public String getAuthority() {
        return user.getRole();
      }
    });
    return collect;
  }

  @Override
  public String getPassword() {
    return user.getPassword();
  }

  @Override
  public String getUsername() {
    return user.getUsername();
  }

  @Override
  public boolean isAccountNonExpired() {
    return true;
  }

  @Override
  public boolean isAccountNonLocked() {
    return true;
  }

  @Override
  public boolean isCredentialsNonExpired() {
    return true;
  }

  @Override
  public boolean isEnabled() {
    return true;
  }

  @Override
  public Map<String, Object> getAttributes() {
    return attributes;
  }

  @Override
  public String getName() {
    return null;
  }
}
```



#### UserDetailsService

- DB에서 사용자 정보를 가져오는 역할

- UserDetailsService 인터페이스 구현하면 loadUserByUsername() 메서드가 오버라이드된다.

  - loadUserByUsername() : DB에서 사용자 정보를 불러오는 중요한 메서드이다
  - 시큐리티 설정에서 loginProcessingUrl("/login")을 했기 때문에, /login 요청이 들어오면 자동으로 loadUserByUsername 함수가 호출된다.

- 화면에서 입력한 사용자의 이름을 가지고 loadUserByUsername() 메서드를 호출하여 DB에 있는 이용자의 정보를 UserDetails 타입으로 가져온다. 사용자가 없다면 예외를 던진다.

  DB에서 가져온 사용자 접오와 화면에서 입력한 로그인 정보를 비교 후 일치하면 Authentication 참조를 리턴하고, 일치하지 않으면 예외를 던진다.

```java
@Service
public class PrincipalDetailsService implements UserDetailsService {

  @Autowired
  private UserRepository userRepository;

  @Override
  public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
    User userEntity = userRepository.findByUsername(username);
    if(userEntity!=null){
      return new PrincipalDetails(userEntity);
    } else {
        throw new UsernameNotFoundException(userEntity);
    }
    return null;
  }
}
```

