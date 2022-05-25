# RabbitMQ와 Spring Boot 프로젝트 간의 커넥션 및 Queue 생성 안됨

Spring AMQP를 사용하여 만든 멀티 모듈을 사용하여 RabbitMQ 클러스터와 연결 및 메세지 큐 테스트를 하였다. 

API 모듈과 Engine 모듈이 있고 API 모듈에서 RabbitMQ로 메세지를 보내면 Engine 모듈에서 가지고 갈 수 있도록 하였다. 

API 모듈을 동작 시키면 해당 모듈에서 메세지를 보낼 큐를 생성하도록 아래와 같이 구현하였다. 

```java
@Bean
  Queue testQueue() throws UnknownHostException {
    String queueName = TEST_QUEUE + "-" + HardwareInformationUtils.getMgmtInterfaceIp();
    return new Queue(queueName, true, false, false);
  }

  @Bean
  public FanoutExchange testExchange() {
    return new FanoutExchange(TEST_EXCHANGE);
  }

  @Bean
  Binding networkBinding(FanoutExchange testExchange, Queue testQueue) {
    return BindingBuilder.bind(testQueue).to(testExchange);
  }
```

처음에는 잘 동작하다가 갑자기 RabbitMQ management와 connection이 연결이 안되고 Queue도 생성이 안되는 문제가 있었다. 

코드 상으로 변경된 것도 없고 설정이 달라지지 않았는데 connection이 갑자기 안됐다.

기존에 API 모듈을 docker image로 만들어서 다른 서버에 동작 시킨 컨테이너가 있었는데 해당 컨테이너를 내리고 다시 실행시켰을 때도 제대로 connection이 맺어지고 Queue도 정상적으로 만들어졌다.

원인을 모르는 이슈는 대체로 Intellij Invalid Cache를 하면 해결됐기 때문에 캐시를 날리고 한 번 더 시도해 보았는데도 여전히 Connection과 Queue가 정상적으로 안 생겼다.

Spring AMQP를 사용할 때 RabbitMQ와 connection이 안맺어지는 원인을 찾다가 아래의 글을 발견하였는데 `spring-boot-starter-actuator`의존성을 추가해주면 된다고 해서 추가해 주었다.

[https://localcoder.org/spring-amqp-rabbitmq-connection-is-not-created-on-application-startup](https://localcoder.org/spring-amqp-rabbitmq-connection-is-not-created-on-application-startup)

그랬더니 웬 걸,,, `spring-boot-stater-actuator` 의존성을 추가하니 정상적으로 connection도 맺어지고 내가 원하는 Queue도 생성이 되었다. 

`spring boot actuator`는 스프링 부트 애플리케이션에서 제공하는 여러가지 정보를 모니터링을 하기 쉽게 해주는 역할을 한다고 한다. 예를 들면 컨텍스트 빈, 환경 설정, 자동 설정, JVM의 상태와 같은 것들을 모니터링 하는 역할을 한다고 한다. 

솔직히 말하면 RabbitMQ Connection과 `spring-boot-stater-actuator` 의존성 간의 관계는 잘 모르겠다…ㅎ

그래도 해당 의존성을 추가해줘서 잘 해결됐당~
