# 구글 로그인



### OAuth

- 별도의 회원가입 없이 로그인을 제공하는 플랫폼의 아이디만 있으면 서비스를 이용할 수 있도록, 외부 서비스에서도 인증을 가능하게 하고, 그 서비스의 API를 이용하게 해주는 것

  - ex) 네이버, 페이스북, 구글, 카카오 로그인

  

## 구글 로그인

1. Google Cloud Platform에서 사용자 인증 API 받아오기

   <img src="https://lh6.googleusercontent.com/uz_8A1XtxpojdaiYREBRdeLlV8l7HlpcSNoB7B_Hu-GLslFEXwSIRUClfpvxxEvSkcUN2gQYrMABFBjmGBWfxKengpCMce9DddBBVtXssJcACyFB5x1Q5fvdCYhWRjc0MEpNnZ9-" alt="img" style="zoom: 67%;" />

   

   <img src="https://lh5.googleusercontent.com/7qU8tHsBuCZs3Nm3wxcG74fbOmcZ2EyvX5KAQeWzRbCvRibsAPKA0NYADQJyBSGMlgyXWBCO3G4Vixn3fc1Z0qHuuk0kSEeiccyfaaKcHVpOjA2IOu_tude3IQa31dhQiK-Cm1l5" alt="img" style="zoom:67%;" />



<img src="https://lh4.googleusercontent.com/oErVzlofEauVca0hlxrCAgfdnZYpgCd24lfb4lOgzwdaGPg_uYyweTQh3V7fImOqiH8NiRxt15zOjPXD_eBWN6Yjyl8XsJtwS62FD9nSj3IPpz9aST1zSVX_bs-X-77M7rOTv-g9" alt="img" style="zoom: 67%;" />



<img src="https://lh6.googleusercontent.com/JRLx-z_9aaOn6n5FkiUqbPAclq6QwTBn3EdhRGFNZdzp3PVwOa_4rgX6UREnNGfLUk1Pe5hD_OILtQrV5X-4isuccJb5S8Vw3wMNRBbJWmcSaSizM35TKs7QtKq8m4DOVxEYpvLd" alt="img" style="zoom:67%;" />



<img src="https://lh6.googleusercontent.com/luzC4cnxLtIJIcTZv7J4iq_0vrSvk39HpCFyn6gbQvmHjy0OK3AaG1jgbnhK6vP5ASRnfYH3gx13Ns6PQbxyy0EWYwtAIAn3jRR9NCupGloapGWgUnlVLLy3k4P_C8hOsPyoLyY_" alt="img" style="zoom:67%;" />



<img src="https://lh6.googleusercontent.com/0LKAU6EFHeN4FduaEz0C2pGVqFk7JSduJYQNTnPV-y5lJ1MXMCCw44QrYkc_aLuMsYdIy7Ykx6m6onyUpUsifM1vciFyTwEVXY3DC43dzMvoOQTQwx3tNVYeqnk0KFNKVAFSFr-n" alt="img" style="zoom:67%;" />



2. oauth dependency 추가

   ```xml
   <dependency>
       <groupId>org.springframework.security</groupId>
       <artifactId>spring-security-oauth2-client</artifactId>
   </dependency>
   ```



3. application.yml 수정

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
   ```

   

4. loginForm.html 수정

   ```html
   <body>
   <h1>로그인 페이지</h1>
   <hr/>
   <form action="/login" method="POST">
     <input type="text" name="username" placeholder="Username"/></br>
     <input type="password" name="password" placeholder="Password"/></br>
     <button>로그인</button>
   </form>
   <a href="/oauth2/authorization/google">구글 로그인</a>
   <a href="/joinForm">회원가입을 아직 하지 않으셨나요</a>
   </body>
   ```

/oauth2/authorization/google 

- 해당 주소는 spring-security-oauth2-client 라이브러리 쓸 때 고정되는 주소이다.

다음과 같이 설정 후 '구글 로그인' 선택 시 404 error(매핑된 페이지가 없기 때문)



5. SecurityConfig.java에 설정 추가

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
           .anyRequest().permitAll() 
           .and()
           .formLogin()
           .loginPage("/loginForm") 
           ...
           .and()
           .oauth2Login()
           .loginPage("/loginForm");
     }
   }
   ```

   .formLogin().loginPage("/loginForm") -> 인증이 필요하면 /loginForm 경로로 이동

   .oauth2Login() -> OAuth2 로그인 기능에 대한 여러 설정의 진입점

   

   

   <img src="https://lh6.googleusercontent.com/ugg3PLJ3BIwkjRDfczrmu0-mdRUm6cdQLD-EW4uoXv0W7jEG781oMPYisbNlC9pRH31kgvH7Gg73ECZ_sLiDnTQdI6dsgtAj0jMITlZhWtjsjCXOLTMV4WrhLykZSUthxUYsP1sn" alt="img" style="zoom:80%;" />

   

   다음과 같이 구글 로그인 화면이 나온다

   구글 계정 선택 후 로그인 후 /manager 접근 시 403 error

   - 로그인 했지만 session이 없기 때문에

   