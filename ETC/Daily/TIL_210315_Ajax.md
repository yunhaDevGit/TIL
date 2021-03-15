# Ajax

- Asynchronous Javascript And Xml(비동기식 자바스크립트와 xml)
- JavaScript를 사용한 비동기 통신, 클라이언트와 서버간에 xml 데이터를 주고 받는 기술



### Ajax 동작 원리

자바스크립트 코드를 통해 웹 서버와 통신

- 사용자의 동작에는 영향을 주지 않으면서, 백그라운드에서 지속해서 서버와 통신할 수 있다.

<img src="http://www.tcpschool.com/lectures/img_ajax_other_application.png" alt="other application" style="zoom:80%;" />

<img src="http://www.tcpschool.com/lectures/img_ajax_ajax_application.png" alt="ajax application" style="zoom:80%;" />![other application](http://www.tcpschool.com/lectures/img_ajax_other_application.png)

1. 사용자에 의한 요청 발생
2. 요청 이벤트 발생하면 이벤트 핸들러에 의해 자바스크립트 호출
3. 자바스크립트는 XMLHttpRequest 객체를 사용하여 서버로 요청(브라우저는 응답을 기다리지 않고, 다른 작업을 할 수 있다.)
4. 서버는 전달받은 XMLHttpRequest 객체를 가지고 Ajax 요청 처리
5. 서버는 처리한 결과를 HTML, XML 또는 JSON 형태의 데이터로 웹 브라우저에 전달
6. 서버로부터 전달받은 데이터를 가지고 웹 페이지의 일부분만을 갱신하는 자바스크립트를 호출
7. 웹 페이지의 일부분만 다시 로딩되어 표시



## $.ajax() 메소드

``` js
$.ajax([옵션])

$.ajax({
    type : 'get', 
    url : "/example/user/hello", 
    dataType : "json", 
    data : {name : "홍길동"},
    beforeSebd : function(xhr){
        xhr.setRequestHeader("Content-Type", "application/json");
    }
})
```

- type : HTTP 요청 방식(GET, POST)
- url :  클라이언트가 요청 보낼 서버의 URL 주소
- dataType : 서버에서 보내줄 데이터의 타입
- data : 서버로 보낼 데이터
- beforeSend : 요청 전 콜백 함수. 요청을 보내기 전에 jqXHR 객체를 수정할 수 있다.
- complete : 요청이 완료되었을 때 호출되는 함수. 요청에 대한 응답(success, error 반환 후 실행)
- success : 요청이 성공했을 때 호출되는 함수



참조 : http://www.tcpschool.com/ajax/ajax_intro_works

