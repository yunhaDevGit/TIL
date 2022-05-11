# RabbitMQ의 주요 개념

## Core Concept

RabbitMQ란 Java로 만든 표준 AMQP를 구현한 오픈 소스 `메세지 브로커 시스템`입니다.

AMQP를 지원하는 메세지 큐 브로커로 플러그인 방식으로 MQTT를 지원합니다.

AMQP를 이용한 범용 메세징 서버라 좀 무겁지만 클러스터링을 지원하며 다양한 플러그인을 이용하여 기능을 확장할 수 있습니다.

STOMP, 웹 소켓, HTTP(S)  등 다양한 통신 프로토콜을 지원하기 때문에, 메세징 서버 인프라 개발에 적절합니다.

RabbitMQ에서 중요한 개념은 Producer, Consumer, Queue, Exchange, Binding이 있습니다.

![image](https://user-images.githubusercontent.com/74949294/166873464-ddabda81-2a92-4f35-b889-d542b73353c4.png)

### Producer

메세지를 생성하고 발송하는 주체입니다. 이 메시지는 Queue에 저장되는데 주의할 점은 Producer는 Queue에 직접 접근하지 않고, 항상 **Exchange를 통해 접근**하게 됩니다.

message를 consumer에게 위임하기 위해 message를 exchange에 publish 합니다.

message 전송하는 일 외에는 아무 일도 하지 않습니다.

### Consumer

메세지를 수신하는 주체입니다. Consumer는 Queue에 직접 접근하여 메세지를 가져옵니다. 

### Message

처리해야 할 내용이 담겨져 있습니다.

### Queue

Queue는 Producer들이 발송한 메세지들이 Consumer가 소비하기 전까지 보관되는 장소입니다. 

Queue는 전달 받은 message들을 연결 된 consumer 들에게 공평하게 Round-Robin 스케줄링 방식으로 나눠줍니다.

Queue는 이름으로 구분되는데 같은 이름과 같은 설정으로 Queue를 생성하면 에러 없이 기존 Queue에 연결되지만, 같은 이름과 다른 설정으로 Queue를 생성하려고 시도할 경우 에러가 발생합니다.

### Exchange

Producer들에게서 전달 받은 메세지들을 적절한 Queue나 다른 Exchange로 분배하는 라우터의 기능을 합니다.  Exchange는 수신한 메세지를 분배하기 위해 Exchange Type이라는 라우팅 알고리즘을 사용합니다. 브로커는 여러 개의 Exchange Type 인스턴스를 가질 수 있습니다. 

Exchange Type은 네 가지가 있으며, 일종의 라우터 개념입니다. 

| 타입 | 설명 | 특징 |
| --- | --- | --- |
| Direct | Routing Key가 정확히 일치하는 Queue에 메세지 전송 | Unicast |
| Topic | Routing key 패턴이 일치하는 Queue에 메세지 전송 | Multicast |
| Headers | [key:value]로 이루어진 header 값을 기준으로 일치하는 Queue에 메세지 전송
(모든 header가 일치할 때, 하나라도 일치할 때 메세지 전송 등의 옵션을 줄 수 있다) | Multicast |
| Fanout | 해당 Exchange에 등록된 모든 Queue에 메세지 전송 | Broadcast |

### Binding

Exchange와 Queue와의 관계를 정의한 일종의 라우팅 테이블입니다. Binding이 되어 있어야 Exchange가 Queue에게 메세지를 전달할 수 있습니다. 

동일한 Queue가 여러 개의 Exchange에 Binding 될 수도 있고 단일 Exchange에 여러 개의 Queue에 Binding 될 수 있습니다.

> Excahnge와 Queue는 m:n binding이 가능합니다.
> 

exchange 규칙으로 어떻게 라우팅이 될 지 결정 되었으면, binding은 결정된 메세지를 어떤 queue에게 전달할 지 라우팅 할 규칙을 결정합니다. 특정 조건에 맞는 메세지를 특정 큐에 전송하도록 설정할 수 있는데, 이는 해당 Exchange 타입에 맞게 설정되어야 합니다. 

> *Exchange Type을 아래의 Binding과 혼동할 수 있는데 **Exchange Type은 받은 메세지를 어떤 방법으로 라우팅 할 지** 정하는 것이고 **Binding은 이러한 방법으로 결정된 메세지를 어느 Queue에 전달할 지 결정하는 라우팅 테이블입니다.***
> 

## Exchange별 동작 원리

### Direct Exchange

Direct Exchange는 라우팅 키를 이용하여 메세지를 라우팅 하는데, 하나의 큐에 여러 개의 라우팅 키를 지정할 수 있습니다. 

![image](https://user-images.githubusercontent.com/74949294/166873309-13caf7a2-d846-4d50-b454-e8717b0a81c0.png)

위 그림은 error 메세지만 저장소에 기록하고 info와 warning을 포함한 모든 정보를 디스플레이에 출력할 때를 나타낸 모식도입니다. (C1 : 저장소, C2 : 디스플레이)

Direct Exchange에 여러 큐에 같은 라우터 키를 지정하여 Fanout처럼 동작하게 할 수도 있습니다.

![image](https://user-images.githubusercontent.com/74949294/166873323-ac8794da-5ba9-4ea0-ab51-7166a1b0cc84.png)

> RabbitMQ는 Direct Exchange가 Default Exchange로 설정되어 있으며 RabbitMQ에서 생성되는 모든 Queue가 자동으로 binding 됩니다. 
이 때, 각 Queue의 이름이 라우팅 키로 지정됩니다.
> 

### Topic Exchange

Topic Exchange는 라우팅 키 패턴에 따라 메세지를 라우팅합니다. 

![image](https://user-images.githubusercontent.com/74949294/166873341-f529f230-9d58-418f-835a-99e3f0a0f797.png)

라우팅 키가 example.orange.rabbit이 경우 메세지가 Q1, Q2 모두에 전달되고, 

라우팅 키가 example.orange.turtle인 경우 메세지가 Q1에만 전달이 됩니다. 

반면 라우팅 키가 lazy.grape.rabbit인 경우 메세지가 Q2에 한 번만 전달됩니다. 

**(라우팅 패턴이 여러개 일치하더라도 하나의 큐에는 메세지가 한 번만 전달됩니다)**

### Headers Exchange

Headers Exchange는 Topic Exchange와 유사하지만 라우팅을 위해 header를 쓴다는 차이점이 있습니다. 

![image](https://user-images.githubusercontent.com/74949294/166873362-1eb11c30-fd63-4842-9ab2-bc88b1695bb5.png)

### Fanout Exchange

Fanout Exchange는 exchange에 등록된 모든 queue에 메세지를 전송합니다. 

![image](https://user-images.githubusercontent.com/74949294/166873407-e808e2dc-e612-403e-b552-47715a488928.png)


## Message Queue 및 Message 보존

RabbitMQ server가 종료 후 재기동하면, 기본적으로 Queue는 모두 제거됩니다. 이를 막기 위해서는 Queue를 생성할 때, Durable 옵션에 true를 주고 생성해야 하며, Producer가 메세지를 발송할 때, PERSISTENT_TEXT_PLAIN 옵션을 주어야 메세지가 보존됩니다. 

**Queue 생성 예시**

```java
//QueueName 다음의 true가 durable option
rabbitmqChannel.queueDeclare(rabbitmqQueueName, true, false, false, null); 
```

**Message 발송 예시**

```java
rabbitmqChannel.basicPublish(exchangeName, routingKey, MessageProperties.PERSISTENT_TEXT_PLAIN, message.getBytes());
```

### Prefetch Count

하나의 Queue에 여러 Consumer가 존재할 경우, Queue는 기본적으로  Round-Robin 방식으로 메세지를 분배합니다. 

그런데, Consumer가 2개인 상황에서 홀수 번째 메세지는 처리 시간이 짧고 짝수 번째 메세지는 처리 시간이 긴 경우, 하나의 Consumer만 계속 일을 하게 되는 상황이 발생할 수 있습니다. 

이를 예방하기 위해 prefetch count를 1로 설정해두면 하나의 메세지가 처리되기 전(Ack를 보내기 전)에는 새로운 메세지를 받지 않게 되므로, 작업을 분산 시킬 수 있습니다. 

[https://blog.dudaji.com/general/2020/05/25/rabbitmq.html](https://blog.dudaji.com/general/2020/05/25/rabbitmq.html)

[https://brownbears.tistory.com/508](https://brownbears.tistory.com/508)
