# Spring Application Events

## 이벤트 처리

Spring에서는 이벤트를 발생 시키고 처리하기 위한 ************************************ApplicationEventPublisher************************************라는 인터페이스가 있는데, 이벤트 처리를 하기 위한 인터페이스를 제공하고 옵저버 패턴의 구현체입니다.

**********************************************ApplicationContext**********************************************는 **************************************ApplicationEventPublisher를 상속 받기************************************** 때문에 `applicationContext.publishEvent()`, `applicationPublishEvent.publishEvent()` 모두 이벤트를 발생시키는 메서드 publishEvent()를 사용 가능합니다.

## 이벤트 핸들러

이벤트 핸들러는 반드시 빈으로 등록이 되어야 합니다. 

Spring 4.2 이전에는 이벤트 핸들러를 등록하기 위해 ************************************************ApplicationListener <E>************************************************ 클래스를 상속 받아야 합니다. 

******onApplicationEvent 메서드******에 ******************************이벤트 객체******************************를 파라미터로 받고 오버라이드 해서 이벤트 발생 시 할 동작을 메서드로 정의할 수 있습니다. 

```java
//event handler는 빈으로 등록해 주어야 한다
//스프링 4.2 이전에는 ApplicationListener<E> 인터페이스를 상속하야함
@Component
public class MyEventHandler implements ApplicationListener<MyEvent> {

    @Override
    public void onApplicationEvent(MyEvent myEvent) {
        System.out.println("이벤트 받았음"+myEvent.getData());
    }
}
```

Spring 4.2 이후에는 이벤트 발생 시 처리하고 싶은 **********동작**********을 ************메서드************에 정의하고 해당 메서드에 **************************@EventHandler************************** 어노테이션만 붙여주면 됩니다.

```java
@Component
public class MyEventHandler {

    @EventListener
    @Async //비동기적으로 실행하고싶다면 @Async 어노테이션을 붙여주면 됨 단, applicaitonRunner에 @EnableAsync도 붙여줘야 멀티스레드 환경에서 돌아감
    @Order(Ordered.HIGHEST_PRECEDENCE) //하나의 이벤트를 처리하는 핸들러가 두개이상일경우 이렇게 순서를 정할 수 있다 (낮을수록 우선순위 숫자로도 줄수 있음)
    public void eventHandleMethod(MyEvent myEvent) {
        System.out.println("이벤트 받았음"+myEvent.getData());
    }
}
```

## 스프링에서 제공하는 기본 이벤트

Spring에서는 ApplicationContext에 관련되어 제공하는 기본 이벤트가 있습니다.

- ********************************************ContextRefreshEvent :******************************************** ApplicationContext를 초기화 했거나 Refresh 했을 경우 발생시킵니다.
- ****************************************ContextStartedEvent :**************************************** start() 메서드를 호출하여 이 이벤트를 트리거하고 ApplicationContext를 시작합니다. 해당 메서드는 일반적으로 명시적 중지 후 빈을 다시 시작하는 데 사용됩니다.
- ********************************ContextStoppedEvent :******************************** ConfigurableApplicationContext에서 stop() 메서드를 호출하여 ApplicationContext가 중지될 때 실행됩니다. start() 메서드를 사용하여 중지된 이벤트를 다시 시작할 수 있습니다.
- ******************************************ContextClosedEvent :****************************************** ConfigurableApplicationContext의 close() 메서드를 사용하여 ApplicationContext가 닫힐 때 실행됩니다. 실제로 컨텍스트를 닫은 후에는 다시 시작할 수 없습니다.
- **********************************************RequestHandledEvent :********************************************** HTTP 요청을 처리했을 때 발생

[https://velog.io/@probsno/Spring에서-이벤트를-처리하는-방법](https://velog.io/@probsno/Spring%EC%97%90%EC%84%9C-%EC%9D%B4%EB%B2%A4%ED%8A%B8%EB%A5%BC-%EC%B2%98%EB%A6%AC%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95)

[https://eblo.tistory.com/165](https://eblo.tistory.com/165)
