# Security Session, Authentication, UserDetails

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
        ...
  }
}

```



시큐리티가 /login 주소 요청이 오면 낚아채서 로그인을 진행시킨다.



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

