# RabbitMQ Message 관리

RabbitMQ에는 `acknowledgment`를 사용하여 Consumer가 메세지를 가지고 온 이후에도 Broker에서 바로 메세지를 제거하지 않고 Consumer가 정상적으로 작업을 마치고 `acknowledgment`를 반환했을 때 Broker에서 메세지를 지울 수 있는 기능이 있다.

만약 Consumer가 작업을 정상적으로 끝내지 못하고 Exception을 발생하면 다시 메세지는 ready 상태가 되어 해당 queue를 바라보고 있는 다른 Consumer에 의해 처리된다.

`acknowledgment`와 `requeue`를 통해 메세지가 유실되지 않고 처리될 수 있다. 

그러나 `acknowledgment`는 하나의 queue를 여러 Consumer가 바라보고 소비할 때 사용하기 적합한데, 나는 queue에 보낼 Action이 특정 서버에서 작업 되어야 하는 Action이었기 때문에 하나의 queue에 하나의 consumer만 connection을 맺었다. 때문에 `acknowledgment` 설정을 필요로 하지 않아서 `application.yml`에 아래와 같이 설정해주었다.

![image](https://user-images.githubusercontent.com/74949294/170420130-3defd601-3890-4775-9540-402b84ea3eab.png)

그러나 Consumer가 메세지를 읽고 작업을 하던 중 작업 중인 모듈(Consumer)이 갑자기 죽어버리거나(Exception이 아니다) 해당 모듈이 동작하던 서버가 죽어버리면 Exception이나 작업의 상태를 업데이트를 할 수 없기 때문에 그대로 메세지도 유실되고 하던 작업도 중단되어 클라이언트는 아무런 상태도 받을 수 없게 된다.

이러한 상황에 대한 설정을 찾고 싶었지만 서버가 죽은 상태에서 exception을 날리거나 상태를 업데이트 할 수 없기 때문에 해당 문제는 RabbitMQ 문제가 아니었고 다른 방법을 찾아봐야 한다. 

---

Consumer에서 메세지를 받으면 해당 Action과 대상에 대해 DB나 File 형태로 저장해두고 만약 작업 도중 서버나 모듈이 죽으면 다시 살아났을 때 해당 노드에서 작업 중이던 Action을 불러와 다시 처리를 하는 방법이 있을 것 같다. 

완료된 작업은 DB나 File에서 지우는 형태로 하면 서버나 모듈이 갑자기 죽었을 때 진행 중이던 Action을 처리할 수 있겠지만 아직 실제로 해본 적은 없다…ㅎ
