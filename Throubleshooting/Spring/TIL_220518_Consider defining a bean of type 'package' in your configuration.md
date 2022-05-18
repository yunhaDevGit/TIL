# **Consider defining a bean of type 'package' in your configuration**

```java
2022-05-18 15:17:42.588 ERROR 605 --- [  restartedMain] o.s.b.d.LoggingFailureAnalysisReporter   : 

***************************
APPLICATION FAILED TO START
***************************

Description:

Parameter 0 of constructor in com.rabbitmq.producer.service.VmService required a bean of type 'com.rabbitmq.core.global.ActionQueueSender' that could not be found.

Action:

Consider defining a bean of type 'com.rabbitmq.core.global.ActionQueueSender' in your configuration.

Disconnected from the target VM, address: '127.0.0.1:47848', transport: 'socket'

Process finished with exit code 0
```

![image](https://user-images.githubusercontent.com/74949294/169012423-61094795-bfe1-4d9b-861f-fa5f8423346d.png)

아래와 같이 의존성 주입을 하였는데도 bean 의존성 주입이 필요하다는 에러가 발생하면서 실행이 되지 않았습니다.

![image](https://user-images.githubusercontent.com/74949294/169012452-614a04ed-81d4-4042-8a78-948b92ac6eea.png)

대체로 의존성 주입 문제가 발생하는 경우가 @Service, @Entity 등 어노테이션을 제대로 안 써줘서 발생했기 때문에 확인해보았지만 전부 잘 작성했었습니다.

해당 서비스가 Gradle 멀티 모듈이고 부모의 bean을 주입 받아야 하는 특수한 상황이라는 것을 생각하지 못하고 있었다. 그러다 의존성 주입이 될 bean의 위치가 공통 모듈에 위치하고 있다는 것을 인지하고 난 뒤에 아래와 같이 자식 모듈에 `@ComponentScan()`을 통해 빈을 읽어올 경로를 추가해주었습니다. 

```java
@SpringBootApplication
@ComponentScan(basePackages = {"com.rabbitmq.core.global","com.rabbitmq.producer"})
public class ProducerApplication {

  public static void main(String[] args) {
    SpringApplication.run(ProducerApplication.class, args);
  }

}
```

해당 어노테이션을 추가하니 정상적으로 동작하는 것을 확인할 수 있었습니다.
