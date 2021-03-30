# 로그인 후 User 정보 가지고 오기



### 일반 로그인 후 User 정보 받아오기

##### IndexController.java

```java
@Controller // view return
public class IndexController {

  @Autowired
  private UserRepository userRepository;

  @GetMapping("/test/login")
  public @ResponseBody String testLogin(Authentication authentication,
      @AuthenticationPrincipal PrincipalDetails userDetails){ // Authentication DI(의존성 주입)
    System.out.println("/test/login ========");
    PrincipalDetails principalDetails = (PrincipalDetails)authentication.getPrincipal();
    System.out.println("authentication : "+ principalDetails.getUser());

    System.out.println("userDeatils : "+userDetails.getUser());
    return "세션 정보 확인하기";
  }
}
```

![img](https://lh4.googleusercontent.com/UjDRq7VgNZIIQy8QRTvl9b3g6F-g87Sbj9hpkzlUtDyGcrWhRwEWTq_JicBD0t_ezwnRgwhar0gp1Nw1DCvslmzLnSPa6VFAOMjyw4IafwakI2oWxgXlhXkCRTd7aag4cI0VWa-R)

1. **Authentication을 의존성 주입**해서 down casting 과정을 통해 User 정보를 가져올 수 있다
   - Authentication 객체 안에 principal이 있다.
   - principal 리턴 타입이 object이므로 PrincipalDetails로 down casting

2. **@AuthenticationPrincipal** 어노테이션을 통해 User 정보를 가져올 수 있다.
   - 세션 정보를 가져올 수 있다.

![img](https://lh5.googleusercontent.com/ya1kTLzAJ8w9T1OFViVuF_u96Qgx562KQ0wIh3BxW5EvmrHSsfh8GglZ02AWJ47BrM5ABqRX9k4EDq_CTp7rP8sGkLmjj1PgyL8zUXG81ZkPUMoJ56zejhbTVncGrldz8UPEaWs9)

> 일반적인 로그인을 하면 UserDetails 타입(=PrincipalDetails)이 Authentication 객체에 들어온다



*구글 로그인 후 위 경로로 접근 시 java.lang.ClassCastException이 발생한다*. PrincipalDetails 타입으로 casting이 안된다...



### 구글 로그인 후 User 정보 받아오기

##### IndexController.java

```java
@Controller // view return
public class IndexController {

  @Autowired
  private UserRepository userRepository;

  @GetMapping("/test/oauth/login")
  public @ResponseBody String testOAuthLogin(Authentication authentication,
                                             @AuthenticationPrincipal OAuth2User oAuth){
    System.out.println("/test/oauth/login ========");
    OAuth2User oAuth2User = (OAuth2User)authentication.getPrincipal();
    System.out.println("authentication : " + oAuth2User.getAttributes());
    System.out.println("oauth2User : " + oAuth.getAttributes());

    return "OAuth 세션 정보 확인하기";
  }
}
```

![img](https://lh5.googleusercontent.com/IGJH8T09RMdhDP3tRZfqVVV-gOP4wdEpz5uOLu0ZNAOEytRvMf2Gm2OHJBFbO2X3uYjVjvBHxhYsQhFtdoJYTq-ik6M1BIHwvok74hWE2lpzO7a6dx3TTvA7SDBWfaLU7kKpbYWv)

- **Authentication** 객체로 정보 접근
-  @AuthenticationPrincipal 어노테이션으로 정보 받아온다
  - **OAuth2User** 타입으로 세션 정보를 받아올 수 있다.

![img](https://lh5.googleusercontent.com/vmslgeiAgOT6zCAMjgrmDtZ6h1bkGf5q-vGKKjW4GbCFXS0xstm5C0q-3kJg9V8sEBdNMQeK4u-QfJONszg-ZMPExb_D7I-vROD1hQu8y25OksnmsDj8afbu8u5pmJDrSDGBRabv)

> OAuth로 로그인을 하면 Authentication 객체에 oauth2User 객체가 들어온다



---

![img](https://lh6.googleusercontent.com/wOZlTdlpaA4Jkq-kCA4NYCM6EdceUbzPtAu_LrcIfNu0nvUq92EukdZzXnGDb4gudD1VHQk6Ky8g9YMwP8P83MLbpuhmJWxOOglBj_7f0fckCvA5et_FdJJ-4QCwKEHGJTS84vqKSNE)

:question: 일반적인 로그인 했을 때는 UserDetails 타입을 받아오고, 구글(OAuth) 로그인을 했을 때는 OAuth2User 타입을 받아오면 controller에서 user 정보 받아올 때는 어떤걸 써야 하지?? :question:

```java
@GetMapping("/user")
public @ResponseBody String user(@AuthenticationPrincipal PrincipalDetails principalDetails) {
  System.out.println("principalDetails : " + principalDetails.getUser());
  return "user";
}

@GetMapping("/user")
public @ResponseBody String user(@AuthenticationPrincipal OAuth2User oAuth) {
  System.out.println("oAuth : " + oAuth.getUser());
  return "user";
}

-> 이걸 어떻게 하면 좋지???
```

Authentication 안에 들어갈 수 있는 타입은 UserDetails아니면 OAuth2User이니까 x라는 클래스를 하나 만들어서 UserDetails와 OAuth2User를 implement 받아서 구현한다.

x라는 타입 자체가 UserDetails나 OAuth2User 타입이기만 하면 담을 수 있다.



```java
@Data
public class PrincipalDetails implements UserDetails {
	...
}
```

PrincipalDetails에서 UserDetails를 implement 받았다. OAuth2User도 PrincipalDetails 객체의 부모로 해서 Authentication 객체 안에 넣으면 어떤 방식으로 로그인을 하던 PrincipalDetails 타입으로 받을 수 있다.

```java
@Data
public class PrincipalDetails implements UserDetails, OAuth2User {
    ...
}
```

 ![img](https://lh5.googleusercontent.com/Ol8D954QUXggAmny2TlCOh83LVbgHBxB6wHTdW7iNPjmZ0TWzT_Emd7N75h0XFO3GrQ4OfzCyeerSZwurGF9GqnpzE271i6-2iYDqPSVyszQs5kUONPiWA2Lq-7HknLwieNMK5vJZAE)