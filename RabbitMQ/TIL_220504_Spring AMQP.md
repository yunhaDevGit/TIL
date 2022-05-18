# Spring AMQP

## Message-Based Communication

메시징은 애플리케이션 간의 커뮤니케이션을 위한 기술입니다. 이는 동기 요청 응답 기반의 아키텍쳐 대신 비동기 메시지 전달에 의존합니다. 

**메세지 프로듀서와 컨슈머는 메세지 브로커라고 하는 중간 메시징 계층에 의해 분리됩니다.**

메세지 브로커는 메세지를 영구적으로 저장하고 필터링 하며 메세지 변환과 같은 기능을 제공합니다. 

Java로 작성된 응용 프로그램 간의 메시징의 경우 일반적으로 JMS(Java Message Service) API가 사용됩니다. **서로 다른 공급 업체와 플랫폼 간의 상호 운용성을 위해서는 JMS 클라이언트와 브로커를 사용할 수 없습니다. 이럴 경우, AMQP가 유용합니다.** 

### AMQP (Advanced Message Queuing Protocol)

RabbitMQ는 AMQP를 따르는 오픈소스 메세지 브로커인데, 메세지를 많은 사용자에게 전달하거나, 요청에 대한 처리 시간이 길 때, 해당 요청을 다른 API에게 위임하고 빠른 응답을 할 때 많이 사용합니다. 또한, Message Queue를 사용하여 애플리케이션 간 결합도를 낮출 수 있는 장점도 있습니다.

AMQP는 플랫폼 중립 바이너리 프로토콜 표준이기 때문에 라이브러리는 다른 프로그래밍 언어로 작성될 수 있으며 다른 환경에서 실행될 수 있습니다. 한 JMS 브로커에서 다른 JMS 브로커로 마이그레이션하는 경우와 마찬가지로 벤더 기반 프로토콜 잠금이 없습니다.

AMQP 브로커는 RabbitMQ, OpenAMQ, StormMQ가 있습니다.

### AMQP Entites

AMQP는 Exchanges, Queues 그리고 Bindings를 만듭니다.

- **Exchanges**는 우체국이나 우체통 같은 것이고 클라이언트들은 AMQP exchange에 메시지를 발행합니다. 4가지 타입의 exchange가 있습니다.
    - Direct Exchange - 라우팅 키를 완벽히 매칭시켜 메시지를 라우팅 합니다.
    - Fanout Exchange - 연결 된 모든 큐에 메시지를 라우팅 합니다.
    - Topic Exchange - 라우팅 키 패턴에 따라 다중 큐에 메시지를 라우팅 합니다.
    - Headers Exchange - 메시지 헤더 기반으로 메시지를 라우팅 합니다.
- **Queues**는 라우팅 키를 사용하여 exchange와 연결합니다.
- **Messages**는 라우팅 키와 함께 exchange에 보내집니다. 그러면 exchange는 message를 Queue에 분산 복사합니다.

### Spring AMQP

Spring AMQP는 두 모듈로 구성되어 있습니다 (spring-amqp, spring-rabbit)

- AMQP entites - Message, Queue, Bindings, Exchange 클래스와 함께 entites를 만듭니다.
- Connection Management - *CachingConnectionFactory*를 사용하여 RabbitMQ 브로커와 연결합니다.
- Message Publishing - *RabbitTemplate*을 사용하여 메세지를 전송합니다.
- Message Consumption - *@RabbitListener*를 사용하여 큐로부터 메시지를 읽어옵니다.

[https://www.baeldung.com/spring-amqp](https://www.baeldung.com/spring-amqp)
