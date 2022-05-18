# login 처리 이상

Spring Security를 활용하여 회원가입과 로그인 기능을 구현하였다.

회원가입 로직은 정상적으로 동작을 하고, DB에도 값이 제대로 들어갔다.

하지만, 회원가입을 한 사용자 계정으로 로그인 시도를 했을 때, 계속해서 로그인이 되지 않았다.

![img](https://lh5.googleusercontent.com/fB8-fOkxnAmAQxz-YVOaeQTsNjzpKc0yk7Gfj9pnxOE_InGdVemJBM9QEmopS29nzKsN2MSOn6wmsDK-NeL9rbQQ5AKiUShbjAjqwa_JVx0f1y4gsY2Mu6Vx3oTCeGwxJxj1lfkO)

다음과 같이 /loginForm?error 만 발생한 채 아무런 변화가 생기지 않았다.

*처음에는 패스워드 암호화 문제라고 생각했다.*

그동안 Spring Security로 로그인 구현을 할 때, 패스워드 암호화를 Bcrypt 방법으로 암호화를 하였었는데, 이번엔 SHA-256 으로 암호화를 구현하였기 때문에 그 과정에서 잘못 구현했을 거라고 생각했다.

그래서 SHA-256으로 구현되어 있었던 패스워드 암호화 부분을 전부 Bcrypt로 변경 후 다시 시도해보았는데, 여전히 같은 문제가 발생했다.



*그냥 /login 요청이 왔을 때, 타는 로직을 디버깅 해보기로 결정했다.*

스프링 시큐리티에서는 로그인 요청이 왔을 경우 낚아채서 로그인 처리를 해주는데, 그 과정에서 UserDetailsService 인터페이스를 사용하여 DB에 있는 사용자 정보를 가지고 온다. 

UserDetailsService는 화면에서 입력한 이용자 이름(username)을 가지고 loadUserByUsername() 메서드를 호출하여 DB에 있는 이용자 정보를 UserDetails 형으로 가지고 오고, 존재하지 않으면 exception을 발생시킨다.

대충 이러한 과정으로 로그인 처리가 동작하는데, 내 프로젝트에서 디버깅을 했을 때 **로그인 요청을 보냈을 때 loadUserByUsername() 메서드를 타지 않았다.** 



....나는 바보다



UserDetailsService 클래스에 @Service 어노테이션을 추가해주지 않아 스프링 시큐리티가 loadUserByUsername을 호출하지 못했던거였다...ㅎ