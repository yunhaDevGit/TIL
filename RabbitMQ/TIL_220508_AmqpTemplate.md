# AmqpTemplate

## AmqpTemplate

`AmqpTemplate` 인터페이스는 메시지를 보내고 받기 위한 모든 기본 작업을 정의합니다.

Spring Framework 및 관련 프로젝트에서 많은 높은 수준의 추상화와 마찬가지로 Spring AMQP 또한 중심 역할을 하는 “template”을 제공합니다. 주요 작업을 정의하는 인터페이스로서 `AmqpTemplate`이라고 불립니다. 이러한 작업은 메세지를 보내고 받는 일반적인 동작을 다룹니다. 즉, 어떤 구현체에도 고유하지 않기 때문에 이름에 “AMQP”가 있습니다. 반면에, AMQP 프로토콜의 구현과 관련된 인터페이스의 구현이 있습니다. interface-level API인 JMS와는 달리 AMQP는 wire-level 프로토콜입니다. 

해당 프로토콜의 구현은 자체 클라이언트 라이브러리를 제공하기 때문에 템플릿 인터페이스의 각 구현은 특정 클라이언트 라이브러리에 따라 다릅니다. 

### 재시도 기능

버전 1.3부터는 `RabbitTemplate`이 `RetryTemplate`를 사용하여 Broker 연결 문제를 처리하도록 구성할 수 있습니다. 다음은 지수 백오프 정책과 기본 `SimpleRetryPolicy`를 사용하는 한 가지 예일 뿐입니다. 이 정책은 호출자에게 예외를 적용하기 전에 세 번 시도합니다.

**`@Configuration` 어노테이션을 사용한 예제**

```java
@Bean
public RabbitTemplate rabbitTemplate() {
    RabbitTemplate template = new RabbitTemplate(connectionFactory());
    RetryTemplate retryTemplate = new RetryTemplate();
    ExponentialBackOffPolicy backOffPolicy = new ExponentialBackOffPolicy();
    backOffPolicy.setInitialInterval(500);
    backOffPolicy.setMultiplier(10.0);
    backOffPolicy.setMaxInterval(10000);
    retryTemplate.setBackOffPolicy(backOffPolicy);
    template.setRetryTemplate(retryTemplate);
    return template;
}
```

1.4 버전부터는 `retryTemplate` 속성과 더불어 `recoveryCallback` 옵션이 `RabbitTemplate`에서 지원됩니다.

```java
retryTemplate.execute(
    new RetryCallback<Object, Exception>() {

        @Override
        public Object doWithRetry(RetryContext context) throws Exception {
            context.setAttribute("message", message);
            return rabbitTemplate.convertAndSend(exchange, routingKey, message);
        }

    }, new RecoveryCallback<Object>() {

        @Override
        public Object recover(RetryContext context) throws Exception {
            Object message = context.getAttribute("message");
            Throwable t = context.getLastThrowable();
            // Do something with message
            return null;
        }
    });
}
```

### 성공 및 실패 감지 방법 (publishing is Asynchronous)

Publishing message는 비동기 매커니즘이며 기본적으로 라우팅 할 수 없는 메세지는 RabbitMQ에 의해 삭제됩니다. 

성공적인 publishing을 위해 비동기식 확인을 아래와 같이 응답 받을 수 있습니다.

**Correlated Publisher Confirms and Returns**

`RabbitTemplate`은 `AmqpTemplate`의 구현으로 publisher 확인 및 반환을 제공합니다. 

반환 된 메세지의 경우 템플릿의 `mandatory` 속성을 반드시 `true`로 설정하거나 `mandatory-expression`이 특정 메세지를 `true`로 평가해야 합니다. `publisherReturns` 속성을 `true`로 하기 위해서는 `CachingConnectionFactory`로 설정되어 있어야 합니다. `setReturnsCallback(ReturnsCallback 콜백)`을 호출하여 `RabbitTemplate.ReturnsCallback`를 등록하여 클라이언트로 반환 됩니다.

```java
void returnedMessage(ReturnedMessage returned);
```

> **ReturnedMessage 속성
- message :** 반환 된 메세지 자체
- **replyCode** : 반환 사유를 나타내는 코드
- **replyText** : 반환 사유에 대한 텍스트
- **exchange** : 메세지가 보내진 exchange(교환기)
- **routingKey** : 사용 된 라우팅 키
> 

각 `RabbitTemplate`은 단 하나의 `ReturnsCallback`만 지원합니다. 

publisher 확인을 위해서는 템플릿에는 `publishConfirm` 속성이 `ConfirmType.CORRELATED` 설정이 되어 있는 `CachingConnectionFactory`가 필요합니다. `setConfirmCallback(ConfirmCallback 콜백)`을 호출하여 `RabbitTemplate.ConfirmCallback`를 등록하여 클라이언트로 확인 메시지를 보냅니다.

```java
void confirm(CorrelationData correlationData, boolean ack, String cause);
```

**CorrelationData**

- 원본 메시지를 보낼 때 클라이언트에서 제공한 객체

**ack**

- 참 : `ack`
- 거짓 : `nack`

**cause**

- 채널이 닫힌 것에 대한 이유

> rabbit template의 send 작업이 완료될 경우 channel이 닫힙니다. 
이는 연결 팩토리 캐시가 가득 찼을 경우 confirm 또는 return 수신을 배제합니다. (캐시에 공간이 있을 경우 채널이 물리적으로 닫히지 않고 confirm 및 return이 정상적으로 진행됩니다)
캐시가 가득 차면 프레임워크는 confirm 및 return을 수신할 시간을 허용하기 위해 최대 5초간 close를 연기합니다. confirm의 경우 마지막 confirm이 수신 되면 채널이 닫히고, 리턴만 사용할 경우 채널은 5초 동안 열린 상태로 유지됩니다. 
일반적으로 `channelCacheSize`를 충분히 크게 설정하여 채널이 닫히지 않고 캐시로 반환 될 수 있도록 하는 것을 권장합니다. 
+ RabbitMQ 관리 플러그인을 사용하여 채널 사용량을 모니터링 할 수 있습니다.
> 

**두 가지 실패 시나리오를 고려**

- exchange에 publish 하지만 일치하는 queue가 없을 경우
- 존재하지 않는 exchange에 publish 하는 경우

첫 번째의 경우 위에 설명한 것처럼 **Correlated publisher Confirms and Returns** 대로 반환 됩니다. 

두 번째의 경우 메시지가 삭제되고 반환이 생성되지 않습니다. 기본 채널은 exception으로 닫힙니다. 기본적으로 exception은 기록되지만 `CachingConnectionFactory`에 `ChannelListener`을 등록하여 이러한 이벤트에 대한 알림을 받을 수 있습니다. 

*ConnectionListener 추가 방법*

```java
this.connectionFactory.addConnectionListener(new ConnectionListener() {

    @Override
    public void onCreate(Connection connection) {
    }

    @Override
    public void onShutDown(ShutdownSignalException signal) {
        ...
    }

});
```

송신 스레드에서 예외를 감지하려면 `RabbitTemplate`에서 `ChannelTransacted(true)`를 설정하면 `txCommit()`에서 예외가 탐지 됩니다.

하지만 **트랜잭션 성능을 크게 방해**하기 때문에 활성화할 때 신중하게 고려해야 합니다.

### Scoped Operations(범위가 지정된 작업)

일반적으로 템플릿을 사용할 때 `Channel`은 캐시에서 체크 아웃(또는 생성) 되어 작업에 사용되며 재사용을 위해 캐시에 반환 됩니다. 다중 스레드 환경에서는 다음 작업이 동일한 채널을 사용한다는 보장이 없습니다. 그러나 채널 사용에 대해 더 많은 제어 권한을 갖고 여러 작업이 모두 동일한 채널에서 수행되도록 하려는 경우가 있습니다. 

2.0 버전부터 `invoke` 함수가 `OperationsCallback`과 함께 제공됩니다. 콜백 범위 내에서 수행된 모든 작업과 제공된 `RabbitOperations` 인수에 대해 동일한 전용 채널을 사용하며, 이 채널은 마지막에 닫힙니다(캐시로 반환하지 않습니다). 만약 채널이 `PublisherCallbackChannel`인 경우 모든 수신이 확인 된 후 캐시로 반환 됩니다.

```java
@FunctionalInterface
public interface OperationsCallback<T> {

    T doInRabbit(RabbitOperations operations);

}
```

기본 채널에서 `waitForConfirms()` 메서드를 사용하려는 경우 이 작업이 필요할 수 있습니다. 이 방법은 이전에 Spring API에 의해 노출되지 않았는데, 이는 채널이 일반적으로 캐시 되고 공유되기 때문입니다. `RabbitTemplate`은 `OperationsCallback`의 범위 내에서 사용되는 전용 채널에 위임하는 `waitForConfirms(long timeout)`과 `waitForConfirmsOrDie(long timeout)`을 제공합니다.

만약 브로커가 confirm을 전달 받을 때까지 기다리길 원할 경우 아래 예제와 같이 할 수 있습니다. 

```java
Collection<?> messages = getMessagesToSend();
Boolean result = this.template.invoke(t -> {
    messages.forEach(m -> t.convertAndSend(ROUTE, m));
    t.waitForConfirmsOrDie(10_000);
    return true;
});
```

`RabbitAdmin` 작업을 `OperationsCallback` 범위 내에서 동일한 채널에서 호출하려면 `invoke` 작업에 사용된 것과 동일한 `RabbitTemplate`를 사용하여 관리자가 구성되어야 합니다.

### Strict Message Ordering in a Multi-Threaded Environment 
(다중 스레드 환경에서의 엄격한 메세지 순서 지정)

RabbitMQ의 비동기적 특성과 캐시 된 채널의 사용으로 인해 동일한 채널이 사용될 지 확실하지 않으므로 메시지가 Queue에 도착하는 순서가 보장되지 않습니다. 이러한 점을 해결하기 위해 채널의 캐시 크기를 1로 지정할 경우(`channgelCheckoutTimeout`과 함께) 메세지가 항상 동일 채널에 publish 되고 순서가 보장되도록 할 수 있습니다. 이렇게 하려면 Consumer와 같이 연결 팩토리를 다른 용도로 사용하는 경우 템플릿 전용 연결 팩토리를 사용하거나 기본 연결 팩토리에 내장된 publisher 연결 팩토리를 사용하도록 템플릿을 구성해야 합니다 

**Spring Boot Application Example**
```java
@SpringBootApplication
public class Application {

	private static final Logger log = LoggerFactory.getLogger(Application.class);

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

	@Bean
	TaskExecutor exec() {
		ThreadPoolTaskExecutor exec = new ThreadPoolTaskExecutor();
		exec.setCorePoolSize(10);
		return exec;
	}

	@Bean
	CachingConnectionFactory ccf() {
		CachingConnectionFactory ccf = new CachingConnectionFactory("localhost");
		CachingConnectionFactory publisherCF = (CachingConnectionFactory) ccf.getPublisherConnectionFactory();
		publisherCF.setChannelCacheSize(1);
		publisherCF.setChannelCheckoutTimeout(1000L);
		return ccf;
	}

	@RabbitListener(queues = "queue")
	void listen(String in) {
		log.info(in);
	}

	@Bean
	Queue queue() {
		return new Queue("queue");
	}


	@Bean
	public ApplicationRunner runner(Service service, TaskExecutor exec) {
		return args -> {
			exec.execute(() -> service.mainService("test"));
		};
	}

}

@Component
class Service {

	private static final Logger LOG = LoggerFactory.getLogger(Service.class);

	private final RabbitTemplate template;

	private final TaskExecutor exec;

	Service(RabbitTemplate template, TaskExecutor exec) {
		template.setUsePublisherConnection(true);
		this.template = template;
		this.exec = exec;
	}

	void mainService(String toSend) {
		LOG.info("Publishing from main service");
		this.template.convertAndSend("queue", toSend);
		this.exec.execute(() -> secondaryService(toSend.toUpperCase()));
	}

	void secondaryService(String toSend) {
		LOG.info("Publishing from secondary service");
		this.template.convertAndSend("queue", toSend);
	}

}
```
