 # 구글 로그인 및 자동 회원 가입



![img](https://lh4.googleusercontent.com/dSws31JZSqCInQI7KSFzUo7oJSZ1lgyLUmm-E7F_mt8XCOlHMDq2DrIUwfro8ac_3E6iVA9OgkZzrEb_f-FzKEa7LQ6wUk0Bxq4OnvjfhFWsYqnnyqEOOa_sy9APhEvTHNnCT2h7jRk)

시큐리티 세션 안에는 Authentication 객체만 들어갈 수 있다.

Authentication 객체가 들고 있는 필드는 두개가 있는데 UserDetails와 OAuth2User 타입이다.

회원가입을 하면 User 객체가 필요한데, OAuth2User와 UserDetails 타입은 User 객체를 포함하고 있지 않는다. OAuth2User와 UserDetails 는 세션에 저장되는데 얘네를 땡겨와도 User 오브젝트를 찾을 수 없다.

그래서 PrincipalDetails라는 클래스를 만들고 UserDetails를 implement 해서 PrincipalDetails에 User를 Autowired를 했다. 

때문에 Session 정보에 접근하면 User 객체에도 접근할 수 있다.



```java
@Entity(name = "user")
@Data
@NoArgsConstructor
public class User {

  ...
    
  @Builder
  public User(String username, String password, String email, String role,
      String provider, String providerId, Timestamp createDate) {
    this.username = username;
    this.password = password;
    this.email = email;
    this.role = role;
    this.provider = provider;
    this.providerId = providerId;
    this.createDate = createDate;
  }
}
```

```java
@Service
public class PrincipleOauth2UserService extends DefaultOAuth2UserService {

	@Autowired
	private BCryptPasswordEncoder bCryptPasswordEncoder;

	@Autowired
	private UserRepository userRepository;

	@Override
	public OAuth2User loadUser(OAuth2UserRequest userRequest) throws OAuth2AuthenticationException {

		// registrationId로 어떤 OAuth로 로그인했는지 확인할 수 있다
		System.out.println("getClientRegistration : "+userRequest.getClientRegistration());
		System.out.println("getAccessToken : "+userRequest.getAccessToken());

		OAuth2User oAuth2User = super.loadUser(userRequest);
		System.out.println("getAttributes : "+ oAuth2User.getAttributes());

		// 회원가입 진행
		// username, password 둘 다 필요 없는데 그냥 만들어 주는 거다
		String provider = userRequest.getClientRegistration().getClientId();
		String providerId = oAuth2User.getAttribute("sub");
		String username = provider+"_"+providerId;
		String password = bCryptPasswordEncoder.encode("갯인데어");
		String email = oAuth2User.getAttribute("email");
		String role = "ROLE_USER";

		User userEntity = userRepository.findByUsername(username);

		if(userEntity==null) {
			System.out.println("구글 로그인이 최초입니다");
			userEntity = User.builder()
                .username(username)
                .password(password)
                .email(email)
                .role(role)
                .provider(provider)
                .providerId(providerId)
                .build();

			userRepository.save(userEntity);
		}else{
			System.out.println("구글 로그인을 이미 한적이 있습니다. 자동회원가입이 되어있습니다");
		}

		return new PrincipalDetails(userEntity, oAuth2User.getAttributes());
        // 얘는 Authentication 객체 안에 들어간다
	}
}
```

> spring security 설정 클래스에서  **.userService(principleOauth2UserService)** 설정을 해주게 되면principleOauth2UserService의 **loadUser**() 라는 함수에서 모든 **후 처리**가 수행된다. 
>
> (-> 구글로부터 받은 userRequest 데이터에 대한 후 처리를 해준다)

PrincipalDetails 

- 일반적인 로그인을 하면 User를 들고 있는다
- oauth 로그인을 하면 User와 Map 형식의 attributes를 들고 있는다



**편해진점**

- 일반 로그인, OAuth 로그인 상관 없이 PrincipalDetails로 받는다.

- Authentication으로 접근을 하면 `OAuth2User oAuth2User = (OAuth2User)authentication.getPrincipal()` 이런식으로 Down Casting을 해야 하는데, @AuthenticationPrincipal으로 접근을 하면   `principalDetails.getUser()` 바로 접근이 가능하다.



**@AuthenticationPrincipal이 활성화 되는 시점** 

PrincipalDetailsService, PrincipleOauth2UserService를 만들지 않아도 기본적으로 **loadUserByUsername, loadUser**는 (오버라이딩 하지 않아도)동작 한다. 그럼에도 두 서비스를 만든 이유는 PrincipalDetails 타입을 리턴하기 위해서이다.

그 리턴되는 타입이 Authentication 객체에 저장된다.

즉, 일반 로그인을 할 때도, Authentication 객체에 PrincipalDetails가 저장(PrincipalDetailsService)이 되고, oauth로 로그인 할 때도 PrincipalDetails가 리턴(PrincipleOauth2UserService에서)되니까 Authentication에 저장이 된다.

