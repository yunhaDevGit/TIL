## Spring Security

**인증, 인가를 지원하고 주요 공격으로부터 어플리케이션을 보호해주는 프레임워크**

**스프링 기반의 어플리케이션 보안을 담당하는 프레임워크**



- 서블릿의 여러 종류의 필터와 인터셉터를 이용해서 처리된다. 
  - 필터 : 서블릿에서 말하는 단순한 필터 의미
  - 인터셉터 : 스프링에서 필터와 유사한 역할
    - 필터와 인터셉터는 서블릿이나 컨트롤러의 접근에 관여한다.
    - 필터는 **스프링과 무관한 서블릿 자원**
    - 인터셉터는 **스프링의 빈으로 관리되면서 스프링의 컨텍스트 내에 속한다**



*스프링 시큐리티를 이용하게 되면 인터셉터와 필터를 이용하면서 별도의 컨텍스트를 생성해 처리*



#### 인증과 권한 부여(Authentication, Authorization)

인증 : 사용자에 대한 검증. 자신을 증명하는 것

권한 부여 : 인증을 기반으로 사이트에 접속 시 컨텐츠 노출 유무에 대한 판단을 하기 위해서 옵션을 주는 개념. 남에 의해서 자격이 부여된다



*Spring Security에서 가장 중요한 역할을 하는 존재가 인증을 담당하는 AuthenticationManager이다*

<img src="https://blog.kakaocdn.net/dn/bLM6OG/btqBAVuWc9N/ldFG9cNkFQIf42F7BNI6Hk/img.png" alt="img" style="zoom:80%;" />





-------

**@EnableWebSecurity**  (스프링 시큐리티 사용을 위한 어노테이션)

- springSecurityFilterChain이 스프링 필터체인에 자동으로 등록된다




SecurityFilterChain

- 브라우저가 서버에 데이터를 요청하면 DispatcherServlet에 전달되기 전 여러 ServletFilter를 거치는데, 이때 Spring Security에 등록된 Filter를 이용해 사용자 보안 관련 처리를 진행한다
- SpringSecurity와 관련된 filter는 연결된 여러 Filter들로 구성되어 있다.

<img src="https://blog.kakaocdn.net/dn/nxFf7/btqBQ0W1aTh/k8pAMBk8K9Z1ayDQbmqcr0/img.png" alt="img" style="zoom:80%;" />

- SecurityFilterChain
  - UsernamePasswordAuthenticationFilter : ID와 Password 사용하는 Form 기반 유저 인증 처리
  - LogoutFilter : 로그아웃 요청 처리
  - SecurityContextPersisteceFilter : SecurityContextRepository에서 SecurityContext를 가져와 유저 Authentication에 접근할 수 있게 함
  - ...



### Spring Security 동작

<img src="https://blog.kakaocdn.net/dn/dOIq8L/btqBOD2WeGT/C9iKmoa4EEKEOTQUJPTDl1/img.png" alt="img" style="zoom:80%;" />

https://bamdule.tistory.com/52

















