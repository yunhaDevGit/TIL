# set annotation dynamic value

RabbitMq queue consumer를 만들 때 @RabbitListener(queue = ?) 를 통해 큐의 메세지를 소비한다. 

MGR node 3대, COM node 3대가 있다고 가정하고 RabbitMQ 구성을 하였다. 

해당 프로젝트는 MSA 구조의 프로젝트인데, NETWORK_API, NETWORK_ENGINE, COMPUTE_API, COMPUTE_ENGINE 이외에도 다양한 모듈이 있었다. RabbitMQ를 사용하여 모듈 간의 메세지를 활용한 비동기 통신 구간을 설계하였는데, 대략적인 구조는 다음과 같다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c5a5dd54-e037-485c-8ca8-380a5a573141/Untitled.png)

`network api`는 `network engine`에 네트워크 관련 요청을 보내고 `compute api`는 `compute engine`에 컴퓨팅 관련 요청을 보낸다. 또한 `network engine`과 `compute engine`간의 메세지 큐 요청도 가능하다. 

컴퓨팅 요청은 대표적으로 vm 생성 작업이 있는데 vm은 com노드 중 메모리 여유가 있는 곳에 생긴다. 그렇기 때문에 특정 노드를 선택하고 해당 노드에 요청을 보내야 했다. 

모든 노드의 rabbitmq를 클러스터링 하였지만 queue는 공유가 되지 않고 queue가 생긴 노드에 종속되기 때문에 queue이름을 `{engine 이름}-{node ip}`로 만들었다. 

엔진을 실행할 때마다 노드 정보를 애플리케이션에 일일이 설정하여 배포 하지 않기 위해 config 파일을 만든 후 해당 config 파일에서 노드 정보를 읽어오는 방식을 채택했다. 

그 과정에서 좀 헤매게 됐는데…

node ip를 동적으로 읽어와서 `@RabbitListener`의 value로 넣어주는 작업이 필요했다.

의존성 주입도 내 맘대로 안됨
→ 생성자 주입에 대해 이해가 조금 부족했던 것 같음

config 클래스에서 원하는 것을 의존성 주입 한 다음에 activeRecevier의 생성자에 넣어주고 해당 클래스에서 원하는 값을 가지고 와서 동적으로 할당해줌

annotation value를 동적 할당 하는 방법은 아래를 참고해서 했다

[https://stackoverflow.com/questions/49909859/dynamic-queues-on-rabbitlistener-annotation](https://stackoverflow.com/questions/49909859/dynamic-queues-on-rabbitlistener-annotation)
