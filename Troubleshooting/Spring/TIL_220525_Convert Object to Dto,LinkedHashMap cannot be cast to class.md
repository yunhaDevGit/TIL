# ~LinkedHashMap cannot be cast to class

Object를 내가 원하는 dto로 convert 하는 과정에서 아래와 같은 오류가 발생했다.

```java
Caused by: java.lang.ClassCastException: class java.util.LinkedHashMap cannot be cast to class com.innogrid.cloudit6.core.domain.network.subnet.dto.SubnetResponseDto (java.util.LinkedHashMap is in module java.base of loader 'bootstrap'; com.innogrid.cloudit6.core.domain.network.subnet.dto.SubnetResponseDto is in unnamed module of loader org.springframework.boot.devtools.restart.classloader.RestartClassLoader @15a23500)
	at com.innogrid.cloudit6.networkengine.global.receiver.ActionReceiver.receive(ActionReceiver.java:37) ~[main/:na]
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:na]
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[na:na]
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:na]
	at java.base/java.lang.reflect.Method.invoke(Method.java:566) ~[na:na]
	at org.springframework.messaging.handler.invocation.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:169) ~[spring-messaging-5.3.16.jar:5.3.16]
	at org.springframework.messaging.handler.invocation.InvocableHandlerMethod.invoke(InvocableHandlerMethod.java:119) ~[spring-messaging-5.3.16.jar:5.3.16]
	at org.springframework.amqp.rabbit.listener.adapter.HandlerAdapter.invoke(HandlerAdapter.java:75) ~[spring-rabbit-2.4.2.jar:2.4.2]
	at org.springframework.amqp.rabbit.listener.adapter.MessagingMessageListenerAdapter.invokeHandler(MessagingMessageListenerAdapter.java:261) ~[spring-rabbit-2.4.2.jar:2.4.2]
	... 13 common frames omitted
```

```java
// ActionDto.java

public class ActionDto() {
	...
	private Object dto;
	...
}
```

```java
// TestService.java
TestDto testDto = TestDto.builder()
										...
										.build();

ActionDto actionDto = ActionDto.builder()
															...
															.dto(testDto)
															.build();

----------------------------------------------------

// Recevie.java
...
TestDto testDto = (TestDto) actionDto.getDto();
```

위와 같이 ActionDto의 Object 타입의 dto를 TestDto로 변환하는 과정에서 위의 에러가 발생했다.

아래와 같이 ObjectMapper를 사용하여 변환해 주었더니 원하는 결과가 잘 나왔다.

```java
ObjectMapper mapper = new ObjectMapper();
TestDto testDto = mapper.convertValue(actionDto.getDto());
```

[http://daplus.net/java-java-lang-classcastexception-java-util-linkedhashmap을-com-testing-models-account로-캐스트-할-수-없습니다/](http://daplus.net/java-java-lang-classcastexception-java-util-linkedhashmap%EC%9D%84-com-testing-models-account%EB%A1%9C-%EC%BA%90%EC%8A%A4%ED%8A%B8-%ED%95%A0-%EC%88%98-%EC%97%86%EC%8A%B5%EB%8B%88%EB%8B%A4/)
