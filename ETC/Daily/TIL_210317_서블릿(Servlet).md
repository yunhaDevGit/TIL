# 서블릿

- 클라이언트의 요청을 처리하고, 그 결과를 반환하는 Servlet 클래스의 구현 규칙을 지킨 자바 웹 프로그래밍 기술
- 클라이언트가 어떠한 요청을 처리하면 그에 대한 결과를 다시 전송해주어야 하는데, 이러한 역할을 하는 자바 프로그램



#### Servlet 특징

1. 클라이언트의 요청에 대해 동적으로 동작하는 웹 어플리케이션 컴포넌트
2. html을 사용하여 요청에 응답
3. Java Thread를 이용하여 동작
4. MVC 패턴에서 Controller로 이용
5. HTTP 프로토콜 서비스를 지원하는 javax.servlet.http.HttpServlet 클래스 상속
6. UDP보다 처리 속도가 느리다
7. HTML 변경 시 Servlet을 재 컴파일 해야 하는 단점



일반적으로 웹 서버는 정적인 페이지만 제공

-> 동적인 페이지를 제공하기 위해서는 다른 곳에 요청하여 동적인 페이지를 작성해야 한다. 

**서블릿은 웹 서버가 동적인 페이지를 제공할 수 있도록 도와주는 어플리케이션이다**

CGI는 동적인 페이지를 생성하는 어플리케이션



**[Servlet 동작 방식]**

<img src="https://t1.daumcdn.net/cfile/tistory/993A7F335A04179D20" alt="img" style="zoom: 67%;" />

1. 사용자(클라이언트)가 URL을 입력하면 HTTP Request가 Servlet Container로 전송
2. 요청을 전송받은 Servlet Container는 HttpServletRequest, HttpServletResponse 객체 생성
3. web.xml을 기반으로 한 사용자가 요청한 URL이 어느 서블릿에 대한 요청인지 찾는다
4. 해당 서블릿에서 service 메소드를 호출한 후 클라이언트의 GET, POST 여부에 따라 doGet() 또는 doPost() 메소드 호출
5. doGet(), doPost() 메소드는 동적 페이지를 생성 후 HttpServletResponse 객체에 응답
6. 응답이 끝나면 HttpServletRequest, HttpServletResponse 두 객체 소멸



### 서블릿 컨테이너

- 서블릿을 관리해주는 것

- 클라이언트의 요청을 받아서 응답할 수 있게 웹서버와 소켓으로 통신한다

  ex) 톰캣(Tomcat)

  - 웹서버와 통신하여 JSP와 Servlet이 동작하는 환경 제공



##### 서블릿 컨테이너 역할

1. 웹서버와의 통신
   - 일반적으로 소켓을 만들고 listen, accept 등을 해야하지만 서블릿 컨테이너는 이 기능을 API로 제공하여 복잡한 과정을 생략할 수 있게 해준다.
2. 서블릿 생명주기(Life Cycle) 관리
3. 멀티쓰레드 지원 및 관리
   - 요청일 올 때마다 새로운 자바 쓰레드를 생성하는데, HTTP 서비스 메소드를 실행하고 나면, 쓰레드는 자동으로 소멸된다.
4. 선언적인 보안 관리
   - 보안에 관련된 내용을 서블릿 또는 자바 클래스에 구현하지 않아도 된다.



**서블릿 생명주기**

<img src="C:\Users\The Jeong\Desktop\TIL\image\ETC\Daily\servlet 생명주기.png" alt="img" style="zoom: 67%;" />

1. 클라이언트의 요청이 들어오면 컨테이너는 해당 서블릿이 메모리에 있는지 확인 후, 없을 경우 init() 메서드를 호출하여 적재. 

   init() 메서드는 처음 한번만 실행되기 때문에, 서블리싀 쓰레드에서 공통적으로 사용해야 하는 것이 있다면 오버라이딩하여 구현. 

   실행 중 서블릿이 변경될 경우, 기존 서블릿을 파괴 후 init()을 통해 새로운 내용을 다시 메모리에 적재한다. 

2. init() 호출 후 클라이언트의 요청에 따라 service() 메서드를 통해 요청에 대한 응답이 doGet(), doPost()로 분기된다. 

   이때, 서블릿 컨테이너가 클라이언트의 요청이 들어오면 가장 먼저 처리하는 과정으로 생성된 HttpServletRequest, HttpServletResponse에 의해 request와 response 객체가 제공된다.

3. 컨테이너가 서블릿에 종료 요청을 하면 destroy() 메서드가 호출된다.

   destroy 역시 한번만 실행되며, 종료시 처리해야 할 작업들은 destroy() 메서드를 오버라이딩하여 구현



#### JSP(Java Server Page)

- Java 코드가 들어가 있는 HTML 코드
- 소스 코드를 수정할 경우에도 디자인 부분을 제외하고 자바 소스코드만 수정하면 되기 때문에 효율이 높다
- 서블릿 규칙이 복잡하기 때문에 JSP가 나왔는데, **JSP는 WAS(Web Application Server)에 의해 서블릿 클래스로 변환**되어 사용된다

<img src="https://mblogthumb-phinf.pstatic.net/20150604_85/islove8587_1433408612779SkNsM_JPEG/4_JSP%C0%C7%B5%BF%C0%DB%B1%B8%C1%B6.jpg?type=w2" alt="img" style="zoom:80%;" />













참고 :  https://mangkyu.tistory.com/14