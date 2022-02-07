# ResponseEntity

ResponseEntity는 **개발자가 직접 결과 데이터와 HTTP 상태 코드를 직접 제어할 수 있는 클래스**로 개발자는 **404나 500 ERROR와 같은 HTTP 상태 코드를 전송하고 싶은 데이터와 함께 전송**할 수 있습니다. 

### ResponseEntity

ResponseEntity에는 body와 헤더 정보, 상태 코드 등을 담을 수 있습니다. 

Spring에서는 HttpEntity라는 클래스가 존재하는데, HTTP 요청 또는 응답에 해당하는 Header, Body를 포함하는 클래스입니다. 

```java
public class HttpEntity<T>{
    private final HttpHeaders headers;
    private final T body;
}
```

ResponseEntity는 HttpEntity의 header, body, 해당 클래스에서의 상태를 가집니다.
