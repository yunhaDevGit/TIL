## This error handler cannot process 'SerializationException's directly

spring-kafka 라이브러리를 사용하여 multi-module 구성으로 kafka producer와 kafka consumer를 구성했다. 

모듈은 다음과 같이 kafka-common, kafka-producer, kafka-consumer 세 가지로 구성되어 있다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b7fa1185-a275-4e47-b3f5-42b0670e4dcf/Untitled.png)

kafka-common은 parent module로 다른 하위 module에 포함됩니다. 

```
kafka-common
	ㄴ config
	ㄴ domain
	ㄴ repository

kafka-producer
	ㄴ config
	ㄴ controller
	ㄴ dto
	ㄴ service

kafka-consumer
	ㄴ ace (message listen)
	ㄴ config
	ㄴ dto
```

그 과정에서 Producer에서 토픽(파티션)에 message를 publish 하는 것까지는 정상적으로 잘 되었으나, Consumer에서 messgae를 소비할 때 'This error handler cannot process 'SerializationException's directly' 에러가 발생했다. 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/449fca9d-45fe-4ce5-b368-3045a8d5e60a/Untitled.png)

해당 에러 메시지 뒤에 'please consider configuring an 'ErrorHandlingDeserializer' in the value and/or key deserializer' 라고 **ErrorHandlingDeserializer** 설정을 하라고 나와 있어 아래와 같이 설정해주었다. 

<수정 전>

```java
@Configuration
public class VmKafkaConsumerConfig extends KafkaConsumerConfig {

  @Value("${kafka.group.ace.vm}")
  private String vmGroupId;

  public ConsumerFactory<String, VmDto> vmConsumerFactory() {
    Map<String, Object> props = consumerFactoryConfig(vmGroupId);
    return new DefaultKafkaConsumerFactory<>(props,
        new StringDeserializer(),
        new JsonDeserializer<>(VmDto.class));
  }

...생략

}
```

<수정 후>

```java
@Configuration
public class VmKafkaConsumerConfig extends KafkaConsumerConfig {

  @Value("${kafka.group.ace.vm}")
  private String vmGroupId;

  public ConsumerFactory<String, VmDto> vmConsumerFactory() {
    Map<String, Object> props = consumerFactoryConfig(vmGroupId);
    return new DefaultKafkaConsumerFactory<>(props,
        new StringDeserializer(),
        new ErrorHandlingDeserializer(new JsonDeserializer<>(VmDto.class)));
  }

...생략

}
```

ErrorHandlingDeserializer는 deserializer에서 발생하는 exception을 확인할 수 있도록 해주는 클래스라고 한다.

해당 클래스를 설정해주었더니 아래와 같은 exception을 확인할 수 있었다.

```
org.springframework.kafka.listener.ListenerExecutionFailedException: Listener failed; nested exception is org.springframework.kafka.support.serializer.DeserializationException: failed to deserialize; nested exception is java.lang.IllegalArgumentException: The class 'io.kafka.cloud.kafkaproducer.dto.VmDto' is not in the trusted packages: [java.util, java.lang, io.kafka.cloud.kafkaconsumer.dto, io.kafka.cloud.kafkaconsumer.dto.*]. If you believe this class is safe to deserialize, please provide its name. If the serialization is only done by a trusted source, you can also enable trust all (*).
	at org.springframework.kafka.listener.KafkaMessageListenerContainer$ListenerConsumer.decorateException(KafkaMessageListenerContainer.java:2387) [spring-kafka-2.7.8.jar:2.7.8]
	at org.springframework.kafka.listener.KafkaMessageListenerContainer$ListenerConsumer.checkDeser(KafkaMessageListenerContainer.java:2434) [spring-kafka-2.7.8.jar:2.7.8]
	at org.springframework.kafka.listener.KafkaMessageListenerContainer$ListenerConsumer.invokeOnMessage(KafkaMessageListenerContainer.java:2302) [spring-kafka-2.7.8.jar:2.7.8]
	at org.springframework.kafka.listener.KafkaMessageListenerContainer$ListenerConsumer.doInvokeRecordListener(KafkaMessageListenerContainer.java:2229) [spring-kafka-2.7.8.jar:2.7.8]
	at org.springframework.kafka.listener.KafkaMessageListenerContainer$ListenerConsumer.doInvokeWithRecords(KafkaMessageListenerContainer.java:2154) [spring-kafka-2.7.8.jar:2.7.8]
	at org.springframework.kafka.listener.KafkaMessageListenerContainer$ListenerConsumer.invokeRecordListener(KafkaMessageListenerContainer.java:2036) [spring-kafka-2.7.8.jar:2.7.8]
	at org.springframework.kafka.listener.KafkaMessageListenerContainer$ListenerConsumer.invokeListener(KafkaMessageListenerContainer.java:1709) [spring-kafka-2.7.8.jar:2.7.8]
	at org.springframework.kafka.listener.KafkaMessageListenerContainer$ListenerConsumer.invokeIfHaveRecords(KafkaMessageListenerContainer.java:1276) [spring-kafka-2.7.8.jar:2.7.8]
	at org.springframework.kafka.listener.KafkaMessageListenerContainer$ListenerConsumer.pollAndInvoke(KafkaMessageListenerContainer.java:1268) [spring-kafka-2.7.8.jar:2.7.8]
	at org.springframework.kafka.listener.KafkaMessageListenerContainer$ListenerConsumer.run(KafkaMessageListenerContainer.java:1163) [spring-kafka-2.7.8.jar:2.7.8]
	at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:511) ~[na:1.8.0_272]
	at java.util.concurrent.FutureTask.run(FutureTask.java:266) ~[na:1.8.0_272]
	at java.lang.Thread.run(Thread.java:748) ~[na:1.8.0_272]
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/195b49d2-2cb4-4ad1-8da1-8def4c1d0b9c/Untitled.png)

'\~failed to deserialize\~ The class 'io.kafka.cloud.kafkaproducer.dto.VmDto' is not in the trusted packages' 라는 메시지를 본 후 message를 publish 할 때 kafka-producer 패키지의 VmDto를 사용해서 메시지가 나온 것 같아, 우선 kafka-common의 Vm entity를 사용하여 publish/consume 하도록 수정해보았다. 

현재 모듈 구조

```
kafka-common
	ㄴ config
	ㄴ domain
			ㄴ Vm
	ㄴ repository

kafka-producer
	ㄴ config
	ㄴ controller
	ㄴ dto
			ㄴ VmDto
	ㄴ service

kafka-consumer
	ㄴ ace (message listen)
	ㄴ config
	ㄴ dto
			ㄴ VmDto
```

kafka-producer 모듈의 message publish 하는 구간

```java
@ComponentScan(basePackages = "io.kafka.cloud.kafkacommon")
@Service
public class VmService {

  @Autowired
  private KafkaTemplate<String, Vm> vmKafkaTemplate;

  @Autowired
  VmRepository vmRepository;

  @Value("${kafka.topic.ace.vm}")
  private String vmTopicName;

  @Transactional
  public String createVm(Vm vm) {

    // db에 넣는 로직
    System.out.println("VmService - createVm");

    Message<Vm> message = MessageBuilder
        .withPayload(vm)
        .setHeader(KafkaHeaders.TOPIC, vmTopicName)
        .build();

    ListenableFuture<SendResult<String, Vm>> future =
        vmKafkaTemplate.send(message);

    future.addCallback(new ListenableFutureCallback<SendResult<String, Vm>>() {

      @Override
      public void onSuccess(SendResult<String, Vm> stringObjectSendResult) {
        System.out.println("Sent message=[" + stringObjectSendResult.getProducerRecord().value().toString() +
            "] with offset=[" + stringObjectSendResult.getRecordMetadata().offset() + "]");
      }

      @Override
      public void onFailure(Throwable ex) {
        System.out.println("Unable to send message=[] due to : " + ex.getMessage());
      }
    });

    return "success";
  }

}
```

kafka-consumer 모듈의 message consume 하는 구간

```java
@Component
public class VmCreateExecutor {

  @KafkaListener(topics = "${kafka.topic.ace.vm}", groupId = "${kafka.group.ace.vm}", containerFactory = "vmKafkaListenerContainerFactory")
  public void listenWithHeaders(@Payload Vm vm, @Headers MessageHeaders messageHeaders) {
    System.out.println("Received Message: " + vm.toString() + " headers: " + messageHeaders);
  }
}
```

message publish/consume 할 때 전달 객체(?)를 kafka-common 모듈의 Vm으로 하니 정상적으로 동작했다. 

실행 결과

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3f583e5f-cbbe-44d5-9e04-70d98de32a9b/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0e4c9059-a60c-4afa-a6aa-a7cfb3c32e42/Untitled.png)

그러나 나는 다른 모듈로 전달 할 때 dto를 사용하고 싶으므로 조금 더 찾아봐야 할 것 같다.
