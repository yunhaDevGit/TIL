# Spring AMQP 소개

## Introduce

Spring AMQP는 AMQP 기반 메세징 솔루션에 Spring 컨셉의 핵심을 적용한 프로젝트입니다.

메세지를 주고 받기 위해 “template”이라는 높은 수준의 추상화를 제공합니다. 또한 메세지 기반 POJO를 제공합니다. 

이러한 라이브러리는 AMQP 리소스 관리를 용이하게 하는 동시에 종속성 주입 및 선언적 구성의 사용을 촉진합니다. 이 모든 경우에 Spring Framework의 JMS 지원과 유사한 점을 볼 수 있습니다. 

## AMQP Abstractions

Spring AMQP는 두 모듈로 구성되어 있습니다.

`spring-amqp` 및 `spring-rabbitmq` 입니다. `spring-amqp` 모듈은 `org.springframework.amqp.core` 패키지를 포함하는데, 이 패키지 내에서 핵심 AMQP "model"을 나타내는 클래스를 찾을 수 있습니다. 

이는 특정 AMQP 브로커에만 해당되는 것이 아닌 포괄적인 추상화를 제공하기 위함입니다. 최종 사용자 코드는 재사용 할 수 있도록 추상화 단계까지만 구현되어 있기 때문에 벤더 간에 이식성이 있습니다. 

위의 추상화를 `spring-rabbitmq`와 같이 특정 브로커를 대상으로 구현한다. 

### Message

Spring AMQP에서는 `Message` 클래스를 보다 일반적인 AMQP 도메인 모델 표현의 일부로 정의합니다. `Message` 클래스의 목적은 본문과 속성을 단일 인스턴스 내에 캡슐화하여 API를 보다 단순하게 만드는 것입니다.

```java
public class Message {

    private final MessageProperties messageProperties;

    private final byte[] body;

    public Message(byte[] body, MessageProperties messageProperties) {
        this.body = body;
        this.messageProperties = messageProperties;
    }

    public byte[] getBody() {
        return this.body;
    }

    public MessageProperties getMessageProperties() {
        return this.messageProperties;
    }
}
```

`MessageProperties` 인터페이스는 ‘messageId’, ‘timestamp’, ‘contentType’ 등과 같은 공통 속성을 정의하는 인터페이스입니다. 심지어 `setHeader(String key, Object value)` 메서드 호출을 통해 사용자 정의 ‘headers’를 확장할 수 있습니다. 

### Exchange

`Exchange` 인터페이스는 Message Producer가 누구에게 보낼지에 대한 AMQP Exchange를 나타냅니다. 가상 호스트에 있는 각 브로커는 고유한 이름과 다른 몇가지 속성을 가집니다.

```java
public interface Exchange {

    String getName();

    String getExchangeType();

    boolean isDurable();

    boolean isAutoDelete();

    Map<String, Object> getArguments();

}
```

여기서 `Exchange`의 타입을 확인할 수 있는데 `ExchangeTypes`로 정의됩니다. 

기본 타입은 다음과 같습니다. `direct`, `topic`, `fanout` 그리고 `headers`. core 패키지를 보면 각 타입의 `Exchange` 인터페이스가 구현되어 있습니다. 각 타입 별로 queue binding을 어떻게 할 지에 대한 `Exchange` 방법이 다릅니다. 

- Direct Exchange - 라우팅 키를 완벽히 매칭시켜 메시지를 라우팅 합니다.
- Topic Exchange - 라우팅 키 패턴에 따라 다중 큐에 메시지를 라우팅 합니다.
- Headers Exchange - 메시지 헤더 기반으로 메시지를 라우팅 합니다.
- Fanout Exchange - 연결 된 모든 큐에 메시지를 라우팅 합니다.

### Queue

`Queue` 클래스는 어떤 메세지 컨슈머가 메세지를 받을지 나타내는 클래스입니다. 

다양한 `Exchange` 클래스와 마찬가지로, 핵심 AMQP 유형을 추상적으로 표현하기 위함입니다. 

```java
public class Queue  {

    private final String name;

    private volatile boolean durable;

    private volatile boolean exclusive;

    private volatile boolean autoDelete;

    private volatile Map<String, Object> arguments;

    /**
     * The queue is durable, non-exclusive and non auto-delete.
     *
     * @param name the name of the queue.
     */
    public Queue(String name) {
        this(name, true, false, false);
    }

    // Getters and Setters omitted for brevity

}
```

생성자는 Queue 이름을 사용합니다. admin template은 고유한 이름의 Queue를 생성하는 방법을 제공할 수 있습니다. 이러한 큐는 다른 ‘임시’ 상황에서 ‘회신 대상’ 주소로 유용하게 사용될 수 있습니다. 그러므로 큐 자동 생성의 'exclusive' 및 'autoDelete' 속성은 모두 'true'로 설정됩니다.

### Binding

Producer가 exchange에 보내고 Consumer는 queue로부터 받는 것을 고려할 때, 큐를 exchange에 연결하는 바인딩은 메시징을 통해 생산자와 소비자를 연결하는 것이 매우 중요합니다. Spring AMQP에서는 이러한 연결을 나타내는 클래스를 `Binding`으로 정의하였습니다. 여기서는 교환을 위한 기본적인 queue 바인딩을 다룹니다. 

**DirectExchange**(고정 라우팅 키)

```java
new Binding(someQueue, someDirectExchange, "foo.bar");
```

**TopicExchange**(라우팅 패턴)

```java
new Binding(someQueue, someTopicExchange, "foo.*");
```

**FanoutExchange**(라우팅 키 없음)

```java
new Binding(someQueue, someFanoutExchange);
```

“fluent API”를 용이하게 하기 위해 `BindingBuilder` 제공

```java
Binding b = BindingBuilder.bind(someQueue).to(someTopicExchange).with("foo.*");
```

`Binding` 클래스의 인스턴스는 연결을 위한 데이터만을 가지고 있습니다. 즉, “active” 컴포넌트가 아닙니다. 그러나 브로커 구성에서 나올 `AmqpAdmin` 클래스는 `Binding` 인스턴스를 사용하여 브로커에서 실제 바인딩 작업을 트리거 할 수 있습니다. 또한, 동일한 섹션에서 볼 수 있듯이 `@Configuration` 클래스 내에서 `Binding` 인스턴스를 Spring의 `@Bean` 어노테이션을 사용하여 정의할 수 있습니다. 

AMQP 관련 빈 정의를 생성하기 위한 접근 방식을 더욱 단순화하고 응용 프로그램 시작 시 AMQP 브로커에 모두 선언되도록 queue, exchange 및 binding을 인식하는 편리한 기본 클래스도 있습니다.

`AmqpTemplate`은 core package 내에도 정의됩니다. 실제 AMQP 메시징 관련 주요 구성 요소 중 하나로 다음에 자세히 설명하겠습니다.

### Connection

실질적인 TCP 연결입니다.

### Channel

가상 연결. 메시지에 대한 작업 단위입니다.

[https://docs.spring.io/spring-amqp/docs/current/reference/html/#amqp-abstractions](https://docs.spring.io/spring-amqp/docs/current/reference/html/#amqp-abstractions)
