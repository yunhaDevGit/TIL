# Intercepter

클라이언트의 요청이 Controller로 가기 전에 중간에 요청을 가로채서 검사하는 모듈 

예를 들어 클라이언트의 요청이 들어왔는데, 로그인을 하지 않아 session이 생성되지 않았다면 Intercepter가 체크하고 로그인 페이지로 돌려보내준다.

<img src="https://t1.daumcdn.net/cfile/tistory/991AAF335BC5A51F24" alt="img" style="zoom:80%;" />



#### HandlerInterceptorAdapter에서 제공하는 4가지 메서드

##### preHandle(HttpServletRequest request, HttpServletResponse reponse, Object handler)

- 컨트롤러 실행 직전에 동작
- 반환 값이 true일 경우 정상적으로 진행 되고, false일 경우 실행 멈춤(컨트롤러 실행 x)
- 전달인자 중 Object Handler는 핸들러 매핑이 찾은 컨트롤러 클래스 객체이다



##### postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView)

- 컨트롤러 진입 후 view가 랜더링 되기 전 수행
- 전달 인자의 modelAndView를 통해 화면 단에 들어가는 데이터 조작이 가능하다



##### afterComplete(HtppServletRequest request, HttpServletResponse response, Object handler, Exception ex)

- 컨트롤러 진입 후 view가 정상적으로 랜더링 된 후 제일 마지막에 실행되는 메서드



##### afterConcurrentHandlingStarted(HttpServletRequest request, HttpServletResponse response, Object handler)

- servlet 3.0부터 비동기 요청이 가능해짐에 따라, postHandle와 afterComplet 메서드를 수행하지 않고, 이 메서드 수행