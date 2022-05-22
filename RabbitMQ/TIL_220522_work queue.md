# Work Queue

`Work Queue`는 시간이 많이 걸리는 작업을 여러 작업자들에게 분배하는데 사용하는 queue입니다.

`Work Queue`는 `resource-intentive`한 `task`를 행하면서 계속 끝나기를 기다리는 **비효율적**인 현상을 방지합니다. 대신 `task`가 나중에 실행되도록 스케줄링 하는 것입니다.

1. `task`를 `message`로 만들어서 큐에 보냅니다.
2. `worker` 프로세스는 백그라운드에서 `task`를 뽑아서 처리합니다.
    
    > worker가 여러 개일 경우, task들은 worker들 사이에 공유됩니다.
    > 
- **장점**
    - 쉽게 병행 처리를 할 수 있게 됩니다.
    - 쉽게 `worker`의 scale-out을 만들어낼 수 있습니다.
    

### Round-robin dispatching

Task Queue는 작업을 쉽게 병렬 처리 할 수 있습니다. 작업 백로그를 구축하는 경우 더 많은 worker를 추가하면 쉽게 확장할 수 있습니다.

먼저 worker 두 개를 동시에 실행시켰을 경우 두 worker 모두 queue에서 메세지를 가지고 옵니다. 

이때, 메세지가 중복 처리되지 않게 하기 위해 Round-robin 방식으로 첫 번째 메세지는 Consumer1에 두 번째 메세지는 Consumer2에 분배됩니다.

**Example)**

```powershell
# shell 1
java -cp $CP Worker
# => [*] Waiting for messages. To exit press CTRL+C
```

```powershell
# shell 2
java -cp $CP Worker
# => [*] Waiting for messages. To exit press CTRL+C
```

```powershell
# shell 3
java -cp $CP NewTask First message.
# => [x] Sent 'First message.'
java -cp $CP NewTask Second message..
# => [x] Sent 'Second message..'
java -cp $CP NewTask Third message...
# => [x] Sent 'Third message...'
java -cp $CP NewTask Fourth message....
# => [x] Sent 'Fourth message....'
java -cp $CP NewTask Fifth message.....
# => [x] Sent 'Fifth message.....'
```

```powershell
# shell 1
java -cp $CP Worker
# => [*] Waiting for messages. To exit press CTRL+C
# => [x] Received 'First message.'
# => [x] Received 'Third message...'
# => [x] Received 'Fifth message.....'
```

```powershell
# shell 2
java -cp $CP Worker
# => [*] Waiting for messages. To exit press CTRL+C
# => [x] Received 'Second message..'
# => [x] Received 'Fourth message....'
```

### Message acknowledgment

`task`를 진행할 때 수 초가 걸릴 수 있습니다. 만약 Consumer 하나가 긴 task를 수행하다가 부분적으로 완료 한 상태에서 갑작스럽게 종료될 경우 어떻게 되는지 궁금할 것입니다.

현재 RabbitMQ에서는 Consumer에 메세지를 전달하는 즉시 message를 삭제합니다. 즉, worker가 죽으면 처리 중이던 message를 잃게 되고 해당 worker에 발송되었지만 아직 처리되지 않은 메세지 또한 잃게 됩니다. 

만약 어떠한 `task`도 잃고 싶지 않은 경우 worker가 죽었을 때 `task`를 다른 worker로 보낼 수 있습니다.

메세지가 손실되지 않도록 RabbitMQ에서는 `message acknowledgments`를 지원합니다. 

`acknowledgment`는 RabbitMQ에게 Consumer가 메세지를 수신하고 처리했음을 알려주기 위해 사용되며 RabbitMQ가 메세지를 삭제할 수 있음을 알려줍니다.

만약 `acknowledgment`를 보내지 않고 Consumer가 죽으면 RabbitMQ는 해당 메세지가 완전히 처리되지 않았다고 생각하고 `re-queue` 합니다. 이로 인해 메세지 손실을 방지할 수 있습니다. 

```java
channel.basicQos(1); // accept only one unack-ed message at a time (see below)

DeliverCallback deliverCallback = (consumerTag, delivery) -> {
  String message = new String(delivery.getBody(), "UTF-8");

  System.out.println(" [x] Received '" + message + "'");
  try {
    doWork(message);
  } finally {
    System.out.println(" [x] Done");
    channel.basicAck(delivery.getEnvelope().getDeliveryTag(), false);
  }
};
boolean autoAck = false;
channel.basicConsume(TASK_QUEUE_NAME, autoAck, deliverCallback, consumerTag -> { });
```

### Message durability

만약 RabbitMQ 서버가 죽으면 `task`를 잃어버리게 됩니다. 

만약 RabbitMQ가 종료되거나 충돌되면 사용자가 지시하지 않는 한 queue와 메세지를 잊어버리게 됩니다. 메세지가 손실되지 않게 하기 위해서는 두가지가 필요합니다. queue와 message가 durable 하다는 것을 표시해야 합니다.

[https://lovecode.tistory.com/84](https://lovecode.tistory.com/84)

[https://www.rabbitmq.com/tutorials/tutorial-two-java.html](https://www.rabbitmq.com/tutorials/tutorial-two-java.html)
