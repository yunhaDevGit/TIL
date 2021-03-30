# 네이버 로그인

1. 네이버 개발자 센터 -> Application -> 애플리케이션 등록

![img](https://lh5.googleusercontent.com/23X9f9dlGa6HvkQyFs8Fw63gpAAwYiE_q36qJXCODKS3RTKgneBzeOeWMHFjW1RA8r9rgl5gu8XKUMTYXDhrP0KQqbdI2OXwH_nqz4fdWru5j-bHR-9Yl-bLiaJDV70pPduL_wxL)



2. 애플리케이션 등록

![img](https://lh5.googleusercontent.com/bs6UjWNvXNgP9-MnF5XQK4xr-mu0MeTZ0HKmRIAiT8L_Sep6JkMnfM9TtshUb42sUoQMOVy6DeAxH6Qv2rjc1h2jpEt0p9ilra5uJapn_wxMk-Am3bAyrkfyrOcz-KRtd5Nv44yg)



![img](https://lh5.googleusercontent.com/dZafsAlao7J6LFeJc77O2qeBE0PiVfs5eaGaXHMnBj6fPB5o4p83s6itWf2KavxdtSXCpMSB_GNFoLeTXjC8UGHUjDCf5SEy0UOzzhZr8Wt3S3dOUt4yFoclUAj44qcbkR6ayjb7)

3. application.yml

   ```yml
     security:
       oauth2:
         client:
           registration:
             google:
               client-id: 225849192289-f0n5arsu5ujvv1pa4vkk3hoalcec1tg9.apps.googleusercontent.com
               client-secret: AOEBPnuni1qrV7wpO4CpYbIB
               scope:
                 - email
                 - profile
   
             naver:
               client-id: nbFuk_4VGACcaT2VD8qS
               client-secret: Avyb_tfTlE
               scope:
                 - name
                 - email
               client-name: Naver
               authorization-grant-type: authorization_code
               redirect-uri: http://localhost:8080/login/oauth2/code/naver
               # 페이스북, 구글, 트위터의 redirect-uri 기본 경로가 /login/oauth2/code이기 때문에 따로 적어주지 않아도 된다
   
           # 네이버는 OAuth2.0 공식 지원대상이 아니라서 provider 설정이 필요하다.
           # 요청주소도 다르고, 응답 데이터도 다르기 때문이다
           provider:
             naver:
               authorization-uri: https://nid.naver.com/oauth2.0/authorize
               token-uri: https://nid.naver.com/oauth2.0/token
               user-info-uri: https://openapi.naver.com/v1/nid/me
               user-name-attribute: response # 회원 정보를 json으로 받는데 response라는 키값으로 네이버가 리턴해준다
   
   ```

   네이버 개발자 센터 - 네아로 개발가이드

   ![img](https://lh4.googleusercontent.com/uEbNroDQA5LWcIG2r6_GMGu7n8asALLopvH_JOfXSFOHsFf8KEQDEngrWoZlqoaylAjQGCpqHv9goDFg9vCFVDe6Axuka2hi6J970mACW0lB6I6uegHfNLS-lg2TBrKuRCNhnxd5)

![img](https://lh5.googleusercontent.com/Nv-0L73kzYV1DlpOsbqgb7TfbKTNsJHbyoIlovq_8bPJwHYzgcDPaf1hdMZCN6DBk14_8EETU3qMMKtJdIoIfeDzWhAXCZSdSnumYoDP6SQenBaIJcOuelOO2S8S-Z7Irlmkzexs)

![img](https://lh6.googleusercontent.com/_Ch7KIY3YDg2QZRU4I4aM1K_NbM_Yr2aRQ3RbB5wfnukAxP0qLGYQYV3EhHFM7W-pD_ewLtGqf_NVzvQHf3XzjimDZmuxKLxaY-Xgm7PLioXe5irmpw_q-YHus8QpTRdhiGLVdc7)



**PrincipleOauth2UserService.java**

```java
@Override
public OAuth2User loadUser(OAuth2UserRequest userRequest) throws OAuth2AuthenticationException {
    ...
    // 회원가입 진행
    OAuth2UserInfo oAuth2UserInfo = null;

    if(userRequest.getClientRegistration().getRegistrationId().equals("google")){
      System.out.println("구글 로그인 요청");
      oAuth2UserInfo = new GoogleUserInfo(oAuth2User.getAttributes());
    } else if(userRequest.getClientRegistration().getRegistrationId().equals("naver")){
      System.out.println("네이버 로그인 요청");
      oAuth2UserInfo = new NaverUserInfo((Map)oAuth2User.getAttributes().get("response"));
    }  else {
      System.out.println("우리는 구글과 네이버만 지원합니다");
    }
    
    ...
}
```



**OAuth2UserInfo.java**

```java
public interface OAuth2UserInfo {

  String getProviderId();
  String getProvider();
  String getEmail();
  String getName();

}
```



**NaverUserInfo.java**

```java
public class NaverUserInfo implements OAuth2UserInfo {

  private Map<String, Object> attributes; // oauth2User.getAttributes()

  public NaverUserInfo(Map<String, Object> attributes){
    this.attributes = attributes;
  }

  @Override
  public String getProviderId() {
    return (String)attributes.get("id");
  }

  @Override
  public String getProvider() {
    return "naver";
  }

  @Override
  public String getEmail() {
    return (String)attributes.get("email");
  }

  @Override
  public String getName() {
    return (String)attributes.get("name");
  }
}
```

