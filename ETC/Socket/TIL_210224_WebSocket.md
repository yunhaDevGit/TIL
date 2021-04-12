# WebSocket

기본적으로 **웹**은 HTTP/HTTPS 방식으로 동작하기 때문에 Stateless&Connectionless한 특징을 갖는다

``` 
- Connectionless : 클라이언트가 서버에 요청하고 서버가 클라이언트에게 응답하면 접속 끊는다
- Stateless : 통신이 끝나면 상태 정보를 유지하지 않는다
```



### WebSocket

웹서버와 웹 브라우저가 지속적으로 연결되어 실시간으로 데이터를 주고 받을 수 있도록 지원한다.

양방향 통신을 할 수 있다.

WebSocket 프로토콜은 처음에는 HTTP를 사용하지만, 연결이 되면 그 후 통신은 WebSocket 독자의 프로토콜로 이루어진다.

- 장시간 접속을 전제로 하기 때문에, 접속한 상태라면 클라이언트나 서버로부터 데이터 송신이 가능하다.



### STOMP

- 기본적인 websocket은 연결된 Socket 정보를 가지고 데이터를 양방향으로 전달 할 수 있다. 

  하지만 그러기 위해서는 개발자가 직접 socket 정보를 관리해줘야 한다.

- Socket의 메세지가 binary 또는 text면 무엇이든지 통신할 수 있다. 

  (즉, 어떤 데이터든 전달 될 수 있으므로 위험성이 있다. )



STOMP에서는 format을 가진 메시지 형식을 직접 정의해줘서 개발자가 websocket을 편하게 이용할 수 있도록 지원한다.

Pub(발행)-Sub(구독) 개념 사용 가능하다.

- 클라이언트가 특정 URI를 구독(Sub) 한다
- 다른 클라이언트 또는 서버가 그 구독한 URI로 메세지를 발행(PUB)하면 STOMP에서 제공하는 Broker가 구독하고 있는 모든 클라이언트에게 메시지를 전달해준다

![img](http://postfiles8.naver.net/MjAxODA5MDdfMTYw/MDAxNTM2MzAxMDAyNjk3.IzEZrsWwH5oMary0NkByA_HGy2-k_JlL4_sp9Mzmv6gg._GdcEeqQV4pdYw6yg24EteMcIJ2Sd1k8FPRCI5f5hk8g.PNG.songintae92/message-flow-simple-broker.png?type=w966)



![image-20210224113426376](C:\Users\The Jeong\AppData\Roaming\Typora\typora-user-images\image-20210224113426376.png)

:heavy_check_mark:registerStompEndPoints : 클라이언트가 서버와 소켓 연결 요청을 보낼 endpoint URI 설정

:heavy_check_mark:configureMessageBroker : WebSocket에서 이용할 PUB-SUB의 정보를 등록해준다

  - setApplicationDestinationPrefixed : PUB 요청을 받아들일 prefix 정보

    ex) 클라이언트가 vm이라는 topic(주제)에 PUB 하고 싶다면 /stomp-broker/vm URI로 요청을 보내면 된다

- enablesSimpleBroker : 해당 경로로 simpeBroker를 등록한다. 해당 경로를 Subscribe하는 client에게 메시지를 전달하는 간단한 작업 수행

  - 첫번째로 정의된 /stomp-broker로 시작하는 메시지만 메시지 핸들러로 라우팅한다고 정의
  - 두번째 정의된 /topic , /monitoring으로 시작하는 주제를 가진 메시지를 핸들러로 라우팅하여 해당 주제에 가입한 모든 클라이언트에게 메시지를 발송



http://blog.naver.com/PostView.nhn?blogId=songintae92&logNo=221354405682&parentCategoryNo=&categoryNo=15&viewDate=&isShowPopularPosts=true&from=search

