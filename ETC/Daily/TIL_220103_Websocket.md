## Websocket이란?

Websocket은 웹 브라우저(클라이언트)와 서버 간의 **full-duplex(양방향), bi-directional(전이중적), persistent connection(지속적인 연결)의 특징**을 갖는 프로토콜입니다.

웹 소켓이 나오기 이전에는 클라이언트의 요청이 없을 경우, 서버로부터 응답을 받을 수 없었습니다.

대부분의 통신은 HTTP를 통해 이루어졌는데, HTTP는 Request/Response 기반의 Stateless protocol로 클라이언트에서 필요할 때 request를 할 때만 서버가 response를 하는 방법으로 통신이 되는 한방향 통신입니다.

웹 소켓은 이러한 단점을 해결하기 위해 나온 서버와 클라이언트 사이에 **양방향 통신 채널을 구축할 수 있는 통신 프로토콜입니다.**

일반 Socket 통신과는 달리 HTTP 80 port를 사용하여 방화벽에 제약이 없습니다. 

브라우에서 호출을 통해 서버의 데이터를 가져가는 기능 뿐만 아니라 서버에서 클라이언트를 호출할 수 있는 기능도 있습니다. 

## Websocket 동작 원리

Websocket 프로토콜은 접속 확립에 HTTP를 사용하지만, 그 후의 통신은 Websocket 독자의 프로토콜로 이루어집니다.

먼저 HTTP 통신을 연결 후 Upgrade 헤더를 보내 양방향 연결로 업그레이드합니다. WebSocket은 하나의 URL을 통해 Connection이 맺어지고, 후에는 해당 Connection으로만 통신한다. 

Handshake가 완료되면 Connection이 유지하기 때문에 가용 port 수 만큼의 Client와 통신할 수 있다.

그러므로 `자주 + 많은 양의 + 지연이 짧아야 하는 통신` 을 할 수록 적합합니다.

![image](https://user-images.githubusercontent.com/74949294/151501280-2466058e-b71c-4d34-8cd5-43c1295c2c8d.png)

붉은 박스 : Opening Handshake

노란 박스 : Data transfer

보라색 박스 : Closing Handshake

1. Opening Handshake
웹 소켓 클라이언트에서 핸드 셰이크(HTTP Upgrade) 전송 후 핸드 셰이크 응답을 받는데, 이때 응답 코드는 **101**입니다.
(101 코드는 *‘프로토콜 전환’* 을 서버가 승인했음을 알리는 코드)

2. Data Transfer
핸드 쉐이크를 통해 웹 소켓 연결이 수립되면, 데이터 전송을 시작합니다. 
클라이언트와 서버가 ‘메시지’라는 개념으로 데이터를 주고 받는데, 메시지는 한 개 이상의 ‘프레임’으로 구성되어 있습니다. 
핸드 셰이크가 끝난 시점부터 서버와 클라이언트는 서로가 살아 있는지 확인하기 위해 heartbeat 패킷을 보내며, 주기적으로 ping을 보내 체크합니다. 
해당 설정은 서버와 클라이언트 양측에서 설정 가능합니다. 
(클라이언트 - heartbeat.incommig과 heartbeat.outgoin
 서버 - .setHeartbeatTime())

3. Close handshake
클라이언트와 서버 모두 커넥션을 종료하기 위한 컨트롤 프레임을 전송할 수 있습니다. 

## SockJS

순수 Websocket만으로 구현 했을 경우, 모바일 크롬 브라우저와 IE에서 동작하지 않는 경우가 있습니다. 

그 외에도 Websocket이 정상적으로 동작이 안될 수 있는데 예를 들면 아래와 같습니다.

1. 모든 클라이언트의 브라우저에서 Websocket을 지원한다는 보장이 없습니다.
2. Server/Client 중간에 위치한 Proxy가 Upgrad 헤더를 해석하지 못해 서버에 전달하지 못할 수 있습니다.
3. Server/Client 중간에 위치한 Proxy가 유휴 상태에서 도중에 Connection을 종료시킬 수도 있습니다. 

이러한 문제를 **Websocket Emulation**을 사용합니다.

- node.js → Socket.io
- Spring - SockJs

Spring Framework는 Servlet 위에서 Server/Client 용도의 SockJS 프로토콜을 모두 지원합니다. 

Cloudit V6.0은 Spring Boot를 사용하기 때문에 SockJS를 사용해서 WebSocket을 구현하였습니다.

### STOMP

STOMP는 text 지향의 message protocol로 websocket을 이용한 message handling을 보다 더 쉽게 만들어줍니다. 

Websocket이나 SockJS의 경우 onMessage function에서 받는 메시지를 모두 handling 해야 된다는 단점이 있는데 이러한 단점을 구독(subscription)과 사용자 구독(user)를 통해 처리할 수 있다는 장점이 있습니다. 

STOMP는 기본적으로 subscribe를 통해 데이터를 전달 받습니다. 

기존의 Websocket이나 SockJS를 이용할 경우, 전달 받는 message의 범위가 매우 광범위하기 때문에, 전달되는 message key 값을 넣어 send/receive에 대한 처리를 따로 해줘야 되는 것이 일반적입니다. 

하지만, STOMP의 경우 send point와 subscribe point가 각각 다르기 때문에 처리를 좀 더 자유롭게 해줄 수 있습니다.

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
