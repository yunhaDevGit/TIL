Websocket을 사용하여 monitoring 기능을 구현하였다. 

telegraf를 사용하여 host의 CPU 사용률, Disk I/O, Memory 등의 정보를 수집한 후 수집 된 데이터를 InfluxDB에 담았다.

monitoring 페이지에 접속 했을 때, InfluxDB에 담긴 정보를 10초 주기로 보여주기 위해 Websocket를 사용하기로 했다. 

## HandshakeInterceptor vs ChannelInterceptor

둘 다 비슷하게 Interceptor로 작용하지만  HandshakeInterceptor는 ServerHttpRequest, ServerHttpResponse, WebSocketHandler를 가져온다. 

WebSocketHandler 안에 handelMessage 메소드가 있지만 HandshakeInterceptor에서 CONNECT / DISCONNECT 프레임을 직접 가지고 올 수 없다. 


### ChannelInterceptor Method

- afterReceiveCompletion(Message<?> message, MessageChannel channel, Exception ex)
    - 수신 완료 후 리소스 정리가 제대로 이루어질 수 있도록 발생한 예외에 관계없이 호출
- afterSendCompletion(Message<?> message, MessageChannel channel, boolean sent, Exception ex)
    - 전송 완료 후 리소스 정리가 제대로 이루어질 수 있도록 발생한 예외에 관계없이 호출
- postReceive(Message<?> message, MessageChannel channel)
    - 메시지가 검색된 후 호출자에게 반환 되기 전에 즉시 호출
- postSend(Message<?> message, MessageChannel channel, boolean sent)
    - 송신 호출 직후에 호출
- preReceive(MessageChannel channel)
    - 수신이 호출되는 즉시 그리고 메시지가 실제로 검색되기 전에 호출
- preSend(Message<?> message, MessageChannel channel)
    - 메시지가 실제로 채널에 전송되기 전에 호출


### ChannelInterceptor 구현

ChannelInterceptor를 사용하기 위해 `WebSocketMessageBrokerConfigure`의 `configureClientInboundChannel` 메소드를 오버라이드 해 Registration에 인터셉터를 추가한다. 

```java
@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig extends AbstractWebSocketMessageBrokerConfigurer {

  @Autowired
  private StompHandler stompHandler;

  ...

  @Override
  public void configureClientInboundChannel(ChannelRegistration registration) {
    registration.interceptors(stompHandler);
  }
}
```

`ChannelInterceptor` 를 상속 받은 StompHandler 클래스를 생성한다

```java
@Component
public class StompHandler implements ChannelInterceptor {

  @Override
  public void postSend(Message message, MessageChannel channel, boolean sent) {
    StompHeaderAccessor accessor = StompHeaderAccessor.wrap(message);
    String sessionId = accessor.getSessionId();
    switch (accessor.getCommand()) {
      case CONNECT:
        System.out.println("connect");
        // 유저가 Websocket으로 connect()를 한 뒤 호출됨
        break;
      case DISCONNECT:
        System.out.println("disconnect");
        // 유저가 Websocket으로 disconnect() 를 한 뒤 호출됨 or 세션이 끊어졌을 때 발생함(페이지 이동~ 브라우저 닫기 등)

        break;
      default:
        break;
    }
  }
}
```

Client가 Websocket connection을 맺을 경우 “connect” 메세지가 connection을 끊을 경우 “disconnect” 메시지를 console에 출력한다.

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/9476ccec-49cf-4c23-9d90-510a20c368d1/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220107%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220107T024016Z&X-Amz-Expires=86400&X-Amz-Signature=14fb4c0826adb1d113392cb947ad7cd0d89164f748ee086c00abdff4d4876435&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)


### References
[1] [https://velog.io/@tlatldms/Spring-Boot-STOMP-JWT-Socket-인증하기#handshakeinterceptor-vs-channelinterceptor](https://velog.io/@tlatldms/Spring-Boot-STOMP-JWT-Socket-%EC%9D%B8%EC%A6%9D%ED%95%98%EA%B8%B0#handshakeinterceptor-vs-channelinterceptor)

[2] [https://gompangs.tistory.com/entry/Spring-boot-Websocket-connect-disconnect-관련](https://gompangs.tistory.com/entry/Spring-boot-Websocket-connect-disconnect-%EA%B4%80%EB%A0%A8)
