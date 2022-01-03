### Websocket이란?

서버와 클라이언트 사이에 양방향 통신 채널을 구축할 수 있는 통신 프로토콜.

동작 방식은 먼저 HTTP 통신을 연결 후 Upgrade 헤더를 보내 양방향 연결로 업그레이드한다. 

HTTP에서 동작하지만, 방식이 HTTP와는 많이 다르다. WebSocket은 하나의 URL을 통해 Connection이 맺어지고, 후에는 해당 Connection으로만 통신한다. Handshake가 완료되면 Connection이 유지하기 때문에 가용 port 수만큼의 Client와 통신할 수 있다.

그러므로 `자주 + 많은 양의 + 지연이 짧아야 하는 통신` 을 할 수록 적합하다.

## 구현 (React + Spring Boot)

### 1. Spring Boot 설정

1) build.gradle 설정

```yaml
implementation 'org.springframework.boot:spring-boot-starter-websocket'
```

2) WebSocketConfig 클래스 생성

```java
@Controller
@Configurable
@EnableWebSocketMessageBroker
public class WebSocketConfig implements WebSocketMessageBrokerConfigurer {

  @Override
  public void configureMessageBroker(MessageBrokerRegistry config) {
    config.enableSimpleBroker("/monitoring");
  }

  @Override
  public void registerStompEndpoints(StompEndpointRegistry registry) {
    registry.addEndpoint("/test").setAllowedOrigins("*").withSockJS();
  }
}
```

- @EnableWebSocketMessageBroker
    - Websocket 서버를 사용한다는 설정
    - websocket 연결 속성에 대한 인터페이스 제공
- configureMessageBroker
    - 한 클라이언트에서 다른 클라이언트로 메시지를 라우팅 할 때 사용하는 브로커를 구성
    - 
- registerStompEndpoints
    - 클라이언트에서 websocket을 접속하는 endpoint를 등록
    - STOMP(Simple Text Oriented Messaging Protocol)
        - 스프링프레임워크의 STOMP 구현체를 사용한다는 의미
        - websocket은 통신 프로토콜이지 특정 주제에 가입한 사용자에게 메시지를 전송하는 기능을 제공하지 않는데, 이를 쉽게 사용하기 위해 STOMP를 사용한다.
        - spring 종속적이며, 구독 방식으로 사용된다.
            - Node.js - scoket.io
            - Spring - socket.js, stomp
