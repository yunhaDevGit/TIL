# Role, Privilege

## User, Role, Privilege Entity

**User**

- 사용자 정보를 포함하는 entity 객체로 UserDetails의 구현체
- User와 Role은 N:N 관계

**Role**

- 시스템에서 관리하는 Role 정보를 저장하는 entity 객체
- Role은 Privilege의 컨테이너로써 역할을 수행하기 때문에 하나의 Role은 여러 개의 권한을 포함
- Role과 Privilege의 관계는 N:N 관계
- Role과 Privilege는 “role_privilege” 매핑 테이블을 통해 관리

**Privilege**

- 시스템에서 관리하는 권한 정보를 저장하는 entity 객체
- Role과 Privilege의 관계는 N:N 관계

## UserDetails

- 인증된 핵심 사용자 정보 (권한, 비밀번호, 사용자명, 각종 상태)를 제공하기 위한 interface
- 기존에 만들어진 시스템에 존재하는 User 클래스가 UserDatils의 구현체가 되면 된다
- 추가적으로 시스템에서 사용자 관리 시나리오에 따라 isAccountNonExpired, isAccountNonLocked, isCredentialsNonExpired, isEnabled 구현하면 된다

```java
@Entity
@Builder
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User implements UserDetails {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    @Column
    private String name;

    @Column
    private String email;

    @Column
    private String password;

    @Column
    private String phoneNumber;

    @Transient
    private Collection<SimpleGrantedAuthority> authorities;

    @ManyToMany(fetch = FetchType.EAGER)
    @JoinTable(
            name = "user_role",
            joinColumns = @JoinColumn(name = "user_id", referencedColumnName = "id"),
            inverseJoinColumns = @JoinColumn(name = "role_id", referencedColumnName = "id")
    )
    private List<Role> roles;

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return this.authorities;
    }

    @Override
    public String getUsername() {
        return this.name;
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
}
```

## UserDetailsService

- **username을 가지고 사용자 정보를 조회하고 session에 저장될 사용자 주체 정보인 UserDetails를 반환하는 인터페이스**
- 각 시스템에서는 커스터마이징을 위한 구현체 클래스를 생성해야 한다
- loadUserByUsername()에서 하는 일
    - username을 가지고 사용자 정보 조회
    - 사용자의 Role과 권한(Privilege)를 조회하여, SimpleGrantedAuthority 목록을 authorites에 세팅
    - Authentication 내부 principal 객체에 UserDetails 객체가 저장된다
    - Authentication 내부 authorities 객체에 사용자의 Role과 권한(Privilege) 정보가 저장된다
    - **UserDetails에 authorities가 세팅 되어 있어야, API 별 role이나 권한 체크를 진행할 수 있다**

```java
@Service
public class CustomUserDetailsService implements UserDetailsService {
    private final UserService userService;

    public CustomUserDetailsService(UserService userService) {
        this.userService = userService;
    }

    @Override
    public UserDetails loadUserByUsername(String email) throws UsernameNotFoundException {
        User user = userService.getUser(email)
                .orElseThrow(() -> new UsernameNotFoundException("User is not found. email=" + email));

        user.setAuthorities(
                Stream.concat(
                        getRoles(user.getRoles()).stream(),
                        getPrivileges(user.getRoles()).stream()
                ).collect(Collectors.toList())
        );

        return user;
    }

    private List<SimpleGrantedAuthority> getRoles(List<Role> roles) {
        return roles.stream()
                .map(Role::getName)
                .map(SimpleGrantedAuthority::new)
                .collect(Collectors.toList());
    }

    private List<SimpleGrantedAuthority> getPrivileges(List<Role> roles) {
        return roles.stream()
                .flatMap(role -> role.getPrivileges().stream())
                .map(privilege -> new SimpleGrantedAuthority(privilege.getName()))
                .collect(Collectors.toList());
    }
}
```

## GrantedAuthority

- **GrantedAuthority는 ID, Password 기반 인증에서 UserDetailsService를 통해서 조회 된다**
- high-level authority라고 부르는 이유는, 어플리케이션 전반에 걸친 권한이기 때문이다. 따라서 특정 도메인에 특화된 권한을 의미하지 않는다.
    - 즉, 시스템 레벨에서 필요한 권한이라고 생각하면 된다
    - 만약 specific한 수 천 개의 role을 자기고 있었다면, 빠르게 메모리를 사용했을 뿐만 아니라, 사용자 인증을 하는데 많은 시간이 걸렸을 것이다
- 도메인 별로 구체적인 권한 체크가 필요한 경우, GrantedAuthority로 관리하지 않고, 각 API 별로 비즈니스 권한을 체크한다
- @PreAuthorize나 @Secured 어노테이션을 사용하여 API나 서비스 별로 시스템 권한 체크는 할 수 있다
- Authentication 클래스에 getAuthorities() 메소드를 통해 인증 받은 사용자의 authorities를 조회할 수 있다

```java
// Authentication 클래스
Collection<? extends GrantedAuthority> getAuthorities();
```

## SecurityContextHolder

- SecurityContext를 보관하는 저장소
- SecurityContext에는 Authentication 인스턴스가 저장된다
- Authentication에는 principal, credentials, authorities가 저장된다
