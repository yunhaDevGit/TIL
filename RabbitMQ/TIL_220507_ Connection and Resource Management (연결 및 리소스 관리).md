# Connection and Resource Management (연결 및 리소스 관리)

AMQP 모델은 일반적이고 모든 구현에 적용할 수 있지만 리소스 관리에 들어갈 때 세부 정보는 브로커 구현에 따라 다릅니다. 따라서 여기서는 `spring-rabbit` 모듈 내에만 존재하는 코드에 초점을 맞춥니다. 

RabbitMQ 브로커에 대한 연결을 관리하기 위한 중심 구성 요소는 `ConnectionFactory` 인터페이스입니다. `ConnectionFactory`는 RabbitMQ를 연결하고 관리하는 인터페이스입니다.

`ConnectionFactory`의 책임은 `com.rabbitmq.client.Connection`으로 감싸져 있는`org.springframework.amqp.rabbit.connection.Connection` 인스턴스 제공을 구현하는 것입니다. 

### Connection Factory

RabbitMQ를 연결하고 관리하는 인터페이스입니다. 세 종류의 connection factory가 있습니다.

- PooledChannelConnectionFactory
- ThreadChannelConnectionFactory
- CachingConnectionFactory : Spring에서 제공하는 ConnectionFactory 객체

처음 두 개는 버전 2.3에서 추가되었습니다. 

대부분의 경우, `PooledChannelConnectionFactory`가 사용되어야 합니다. 

`ThreadChannelConnectionFactory`는 범위 지정 작업(Scoped Operations)을 사용할 필요 없이 엄격한 메세지 순서를 보장하고자 할 경우 사용할 수 있습니다.  `CachingConnectionFactory`는 관련된 publisher 확인을 사용하거나 `CacheMode`를 통해 여러 연결을 열고자 할 때 사용해야 합니다.

간단한 publisher 확인은 모든 factory에서 가능합니다. 

RabbitTemplate에서 별도의 연결을 사용하도록 구성할 때 버전 2.3.2부터 게시 연결 팩토리를 다른 유형으로 구성할 수 있습니다.

기본으로 publishing factory는 동일한 유형이며 기본 팩토리에 설정된 모든 속성도 publishing factory에 전파됩니다. 

### PooledChannelConnectionFactory

이 팩토리는 Apache Pool2를 기반으로 단일 연결과 두개의 채널 풀을 관리합니다.

하나의 Pool은 transactional 채널이고 다른 하나의 Pool은 non-transactional 채널입니다. 풀은 기본 구성의 GenericObjectPool이며, 풀을 구성하기 위한 콜백이 제공됩니다. 자세한 내용은 Apache 설명서를 참조하십시오.

이 팩토리를 사용하려면 Apache `commons-pool2` 경로가 있어야 한다. 

```java
@Bean
PooledChannelConnectionFactory pcf() throws Exception {
    ConnectionFactory rabbitConnectionFactory = new ConnectionFactory();
    rabbitConnectionFactory.setHost("localhost");
    PooledChannelConnectionFactory pcf = new PooledChannelConnectionFactory(rabbitConnectionFactory);
    pcf.setPoolConfigurer((pool, tx) -> {
        if (tx) {
            // configure the transactional pool
        }
        else {
            // configure the non-transactional pool
        }
    });
    return pcf;
}
```

### ThreadChannelConnectionFactory

이 factory는 단일 연결과 두 `ThreadLocal`을 관리합니다. 하나는 트랜잭션 채널이고 다른 하나는 비트랜잭션용 채널입니다. 이 factory는 동일 스레드의 모든 작업이 동일 채널을 사용하도록 합니다. 이렇게 하면 scoped operation 없이도 엄격한 메시지 순서를 지정할 수 있습니다. 메모리 누수 방지를 위해 애플리케이션이 짧은 수명의 스레드를 많이 사용할 경우 `closeThreadChannel()`를 호출하여 채널 리소스를 해제해야 합니다. 2.3.7 버전부터 스레드는 채널을 다른 스레드에게 전송할 수 있습니다. 

### CachingConnectionFactory

CachingConnectionFactory는 기본적으로 응용 프로그램에서 공유할 수 있는 단일 연결 프록시를 설정합니다. AMQP와의 메시징을 위한 “작업 단위”가 실제로 “채널”이기 때문에 연결 공유가 가능합니다. 연결 인스턴스는 `createChannel` 메서드를 제공합니다. `CachingConnectionFactory` 구현은 이러한 채널의 캐싱을 지원하며, 채널이 트랜잭션인지 여부에 따라 별도의 캐시를 유지합니다. 만약 `CachingConnectionFactory`를 사용하여 인스턴스 생성 시,  생성자를 통해 ‘hostname’을 제공할 수 있습니다.

또한 ‘username’과 ‘password’ 속성도 제공해야 합니다. `setChannelCacheSize()` 메서드를 통해 채널 캐시 크기를 구성할 수 있습니다.

[https://docs.spring.io/spring-amqp/docs/current/reference/html/#connections](https://docs.spring.io/spring-amqp/docs/current/reference/html/#connections)
