# Spring Security 권한 별 접근제한 시 Role 매칭 이슈

WebSecurityConfigurerAdapter를 상속 받은 Spring Security 설정 파일인 SecurityConfig class의 configure를 다음과 같이 작성하였다.

![img](https://lh3.googleusercontent.com/AB3umZsrPYLtjf0HdiRA-nyEwRM81X1TTsDeat_JfirQPUH6-imZAmWHtvcm9VtOkGrPpbismLM6TIT8DnnQMejjL--a0mTtzxgu9ygVr8Pq6oDE8-t6ZPupG3I4rkXmWcNlt9yd)

이런식으로 각 권한 별(ADMIN, MANAGER, USER) 접근 제한을 설정하였다.

DB에도 다음과 같이 계정에 권한이 들어갈 수 있도록 Role이라는 컬럼을 만들어주었다.

![img](https://lh4.googleusercontent.com/25pKdTQGVVWZeTdSkP59PcGXYvrfa2WMefp9gHTF71mkdXjQKQLxN2Zg8J3cxmE1Gjb5dPKJ6ukNmHJNxq6blPwJjnVJxJ_DAWV-cmKQGL3feexEmHPVsf0jl3lTTFKR0jxNbbh7)

이런식으로 설정한 후, 실행해보았는데 admin 계정으로 로그인 후 '/board/create'로 요청을 보낼 경우 계속해서 403 permission error가 떴다.  분명 ADMIN과 MANAGER에게 해당 url 접근 권한을 주었는데 계속해서 403 에러가 떠서 이상해서 이 문제에 대해 검색을 해보았다.

이유는 바로 Role의 형식(?)문제였다.

SecurityConfig에서 hasRole에 hasRole("ADMIN"),hasRole("MANAGER"),hasRole("USER") 이런식으로 작성하였을 경우, Spring Security에서는 Role_이라는 prefix가 자동으로 붙게 된다.

그렇기 때문에 role column에 들어있는 ADMIN, MANAGER, USER와 매칭되지 않아 403 에러가 발생했던 것이었다.

ADMIN, MANAGER, USER였던 enum 타입을 각각 ROLD_ADMIN, ROLD_MANAGER, ROLE_USER로 수정하였더니 정상적으로 동작하였다.