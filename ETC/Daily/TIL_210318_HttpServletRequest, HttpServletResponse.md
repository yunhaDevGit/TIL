# HttpServletRequest, HttpServletResponse

![img](https://t1.daumcdn.net/cfile/tistory/9985D2485C52B72E2F)

#### WAS가 웹 브라우저로부터 Servlet 요청을 받으면

1. 요청을 받을 때, 전달 받은 정보를 HttpServletRequest 객체를 생성하여 저장
2. 웹 브라우저에게 응답을 돌려줄 HttpServletResponse 객체 생성(빈 객체)
3. 생성된 HttpServletRequest(정보가 저장된)와 HttpServletResponse(비어 있는)를 Servlet에게 전달



#### HttpServletRequest

1. Http 프로토콜의 request 정보를 서블릿에게 전달하기 위한 목적으로 사용
2. Header 정보, Parameter, Cookie, URL, URI 등의 정보를 읽어들이는 메소드를 가진 클래스
3. Body의 Stream을 읽어들이는 메서드를 가지고 있다.

- request 객체는 웹 브라우저에서 JSP 페이지로 전달되는 정보의 모임으로 HTTP 헤더와 HTTP 바디로 구성되어 있다.

- 웹 컨테이너는 요청된 HTTP 메시지를 통해 HttpServletRequest 객체를 얻어내서, 이로부터 사용자의 요구사항을 얻는다.

  - 예를 들어, 아이디, 비밀번호 등의 데이터를 컨트롤러로 보냈을 때, HttpServletRequest 객체 안에 모든 데이터들이 들어간다.

    원하는 데이터를 꺼낼때는 HttpServletRequest 객체의 getParameter() 메서드를 이용하면 된다.



#### HttpServletResponse

1. Servlet은 HttpServletResponse 객체에 Content Type, 응답 코드, 응답 메시지 등을 담아서 전송

- 클라이언트에게 데이터를 전송하기 위해서 사용한다. 



#### HttpSession

**getSession**

- HttpSession 객체를 직접 얻는다.
- 내부적으로는 HttpServletRequest 객체를 먼저 구한 다음에 그 객체로부터 HttpSession 객체를 얻는다.



|        객체        |                 생성                 |             소멸             |
| :----------------: | :----------------------------------: | :--------------------------: |
|   ServletContext   |        웹 application 시작 시        |    웹 application 종료 시    |
|    HttpSession     | 세션을 사용하는 content 최초 접속 시 | 타임아웃 invalidate() 호출시 |
| HttpServletRequest |         해당 servlet 요청 시         |         요청 완료 시         |

|        객체        |               속성 사용                |
| :----------------: | :------------------------------------: |
|   ServletContext   | 동일 application 내에서 모두 사용 가능 |
|    HttpSession     |     동일 Session 내에서 사용 가능      |
| HttpServletRequest |     동일 Request 내에서 사용 가능      |

------



#### HttpServletRequest, HttpSession 차이

HttpServletRequest와 HttpSession의 차이점은 거의 없으나, 세션 객체를 얻는 방법에 차이가 있다.

- HttpServletRequest는 파라미터로 HttpServletRequest를 받은 후 getSession()으로 세션을 얻는다.
- HttpSession은 파라미터로 HttpSession을 받아 세션을 바로 사용한다.



-----

#### Session

- 서버와 클라이언트 사이의 연결 유지
- 세션은 서버에 정보를 저장
- 스프링은 MVC에서 HttpServletRequest를 이용해서 세션을 이용하려면 컨트롤러의 메서드에서 파라미터로 HttpServletRequest를 받으면 된다.



##### URL, URI

URL(Uniform Resouce Locator) : 실제 파일 위치

URI(Uniform Resouce Identifer) : 파일의 위치를 알 수 있는 식별자



참고 : https://zester7.tistory.com/33