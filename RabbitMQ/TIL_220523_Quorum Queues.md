# Quorum Queues

Quorum queue는 Raft Consensus Algorithm 기반으로 HA구성이 가능한 FIFO 큐를 구현하는 RabbitMQ의 큐 유형입니다. Quorum Queue의 최종 목적은 안정성을 기반으로 한 신뢰성 높은 queue를 목표로 합니다. Quorum Queue는 복제 queue의 기본이라고 생각하면 됩니다. 

- **[What are quorum queues](https://www.rabbitmq.com/quorum-queues.html#motivation) and why they were introduced**
- **[How are they different](https://www.rabbitmq.com/quorum-queues.html#feature-comparison) from classic queues**
- **Primary [use cases](https://www.rabbitmq.com/quorum-queues.html#use-cases) of quorum queues and when not to use them**
- **How to [declare a quorum queue](https://www.rabbitmq.com/quorum-queues.html#usage)**
- **[Replication](https://www.rabbitmq.com/quorum-queues.html#replication)-related topics: replica management, [replica leader rebalancing](https://www.rabbitmq.com/quorum-queues.html#replica-rebalancing), optimal number of replicas, etc**
- **What guarantees quorum queues offer in terms of [leader failure handling](https://www.rabbitmq.com/quorum-queues.html#leader-election), [data safety](https://www.rabbitmq.com/quorum-queues.html#data-safety) and [availability](https://www.rabbitmq.com/quorum-queues.html#availability)**
- **[Performance](https://www.rabbitmq.com/quorum-queues.html#performance) characteristics**
- **[Poison message handling](https://www.rabbitmq.com/quorum-queues.html#poison-message-handling) provided by quorum queues**
- **[Configurable settings](https://www.rabbitmq.com/quorum-queues.html#configuration) of quorum queues**
- **Resource use of quorum queues, most importantly their [memory footprint](https://www.rabbitmq.com/quorum-queues.html#resource-use)**

### Motivation

Quorum queue는 더 안전하고 사용자가 시스템을 설계하고 운영할 때 더 쉽게 추론할 수 있는 간단하고 잘 정의된 오류 처리 의미를 제공하도록 설계되었습니다.

이러한 디자인 선택에는 제약이 따르는데 이 부분은 아래에서 다루도록 하겠습니다.

### What is a Quorum?

간단히 말하면, 분산 시스템에서 quorum은 여러 노드 간의 합의를 할 수 있는 것입니다. queue 미러링에 적용되는 경우 RabbitMQ 클러스터는 대부분의 복제본(현재 선택된 대기열 리더 포함)이 queue 및 해당 내용의 상태에 동의함을 의미합니다.

### Differences from Classic Mirrored Queues

Quorum Queue는 RabbitMQ의 다른 타입의 queue의 많은 기본 사항을 공유합니다. 하지만 해당 큐들은 데이터 안정성, 예측 가능한 복구에 초점을 맞춰 개발되었고, 특정 기능을 제공하지 않습니다. RabbitMQ의 Classic Mirrored queue는 이해 가능한 보장 및 명확한 오류 처리 의미를 제공하기 어려운 기술적 제한이 있습니다. 

특정 오류 시나리오로 인해 Mirrored Queue가 메시지를 너무 일찍 확인하여 잠재적으로 데이터 손실을 초래할 수 있습니다.

---

Classic Queue에는 클러스터의 노드 어딘가에서 실행되는 마스터가 있는 반면 mirror는 다른 노드에서 실행됩니다. 이것은 쿼럼 큐에서도 동일하게 동작합니다. 리더는 기본적으로 자신을 생성한 클라이언트 애플리케이션이 연결된 노드에서 실행되고 팔로워는 클러스터의 나머지 노드에 생성됩니다. 

[https://www.erlang-solutions.com/blog/rabbitmq-quorum-queues-explained-what-you-need-to-know/](https://www.erlang-solutions.com/blog/rabbitmq-quorum-queues-explained-what-you-need-to-know/)

### Feature Comparison with Regular Queues

Quorum Queue는 많은 다른 큐들과 기본 사항을 공유합니다. 기본 mirrored queue를 사용할 수 있는 Client Library는 quorum queue도 사용할 수 있습니다. 

Regular Queue와 마찬가지로 Quorum Queue 또한 아래와 같이 동일한 방식을 사용합니다.

- Consumption (subscription)
- Consumer acknowledgements
- Cancelling consumers
- Purging
- Deletion

일부 queue 동작과는 다른 방식도 있습니다

- Declaration
- Setting prefetch for consumers

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8a84368b-890d-416f-8a6c-f24db6029c39/Untitled.png)

### Non-durable Queues

Regular queue는 지속 가능하지 않을 수 있습니다. Quorum queue는 항상 지속 가능합니다. 

### Exclusivity

Exclusive queue는 연결을 선언하는 곳에서 lifecycle에 연결됩니다. Quorum queue는 복제되고 지속 가능하도록 설계되어 exclusive 설정은 의미가 없습니다. 그러므로 quorum queue는 exclusive(독점) 설정이 없습니다. 

### TTL (RabbitMQ 3.10 이전)

Quorum Queue는 [Queue TTL](https://www.rabbitmq.com/ttl.html#queue-ttl)을 지원하지만, message TTL은 지원하지 않습니다.

### TTL (RabbitMQ 3.10 이후)

Queue TTL과 message TTL(Queue의 Per-Queue MessageTTL, Publisher의 Per_Message TTL 포함) 모두 지원합니다. message TTL을 사용하면 메세지당 메모리 오버헤드가 2바이트씩 추가됩니다. 

### [Length Limit](https://www.rabbitmq.com/maxlength.html)

Quorum queue는 queue length limits를 제공합니다.

`drop-head`와 `reject-publish`는 지원하지만 Quorum Queue는 classic queue와 다른 방식으로 동작하기 때문에 `reject-publish-dlx` 설정은 지원하지 않습니다. Quorum Queue가 max-length 제한에 도달하고 `reject-publish` 가 구성되면 각 publishing 채널에 누가 클라이언트에게 모든 메세지를 거부할 지 알려줍니다.  즉, 채널이 알림 되는 동안 전송 중인 메시지가 있을 수 있으므로 쿼럼 큐가 제한보다 적은 수의 메시지로 초과될 수 있습니다. queue에 의해 허용되는 추가 메시지 수는 해당 시간에 전송 중인 메시지 수에 따라 달라집니다.

### Dead Lettering

Quorum queue는 `dead letter exchange`를 지원합니다.

전통적으로 클러스터 환경에서 DLX를 사용하는 것은 안전하지 않습니다. 때문에 RabbitMQ 3.10 qourum queue는 queue간의 메세지 이동을 `at_least_once`를 사용하여 dead-lettering을 좀 더 안전하게 사용할 수 있도록 지원합니다. 

Source Quorum Queue는 확인될 때까지 dead-lettered 메세지를 보관하고 있습니다. 내부 Consumer는 dead-letter 메세지를 소비하고 publisher의 확인을 통해 target queue에 publish 합니다.

`at-most-once`는 Quorum Queue에 대한 기본 dead-letter-strategy이며, dead lettered message가 더 많은 정보를 제공하는 시나리오에 유용하며 queue간 전송 중 손실 여부가 중요하지 않습니다.

### Enabling at-least-once dead-lettering

source quorum queue에 dead-lettering `at-least-once`를 활성화 하려면 아래와 같은 정책이 모두 적용됩니다. 

- `dead-letter-startegy`를 `at-least-once` 설정 합니다(default : `at-most-once`)
- `overflow`를 `reject-publish`로 설정합니다 (default : `drop-head`)
- `dead-letter-exchange` 구성합니다.
- `stream_queue`를 활성화 합니다.

source quorum queue에 과도하게 메세지가 쌓이는 것을 방지하기 위해 `max-length`나 `max-length-bytes` 설정을 추가하는 것을 추천합니다. 

### Limitations

`at-least-once` dead lettering은 queue length limit이 설정되지 않았을 때도 `drop-head`가 기본으로 동작하지 않습니다. 따라서 `drop-head`가 구성된 경우 dead-lettering은 `at-most-once`로 대체됩니다. 

### Caveats (주의 사항)

`at-least-once` dead-lettering은 메모리와 CPU와 같은 시스템 자원을 더 많이 필요로 합니다 .그러므로 dead lettered message가 반드시 유실되면 안될 때만 `at-least-once`를 활성화 하십시오.

`at-least-once` 처리되어야 하는 몇 가지 특정 실패 사례를 열어 줍니다. dead-lettered message는 dead-letter target queue에서 안전하게 수락 될 때까지 source quorum queue에 보관되므로  max length limit, queue resource limit을 통해 큐가 제거 될 때까지 추가 메세지를 거부할 수 있도록 할 수 있습니다. 이론적으로 dead letter queue가 오랫동안 메세지를 수신할 수 없고, 일반 queue consumer가 대부분의 메세지를 소비하는 경우 queue는 dead-lettered message만 포함할 수 있습니다. 

Dead-lettered message는 dead-letter target queue에서 확인할  때까지 “live”를 포함하고 있습니다.

- dead-letter exchange는 존재하지 않습니다.
- 메세지는 queue로 라우팅 될 수 없습니다.
- 라우팅된 대상 queue중 하나는 메세지를 확인하지 않습니다. 이는 target queue가 없거나 target queue가 메세지를 거부했을 경우 발생합니다.

dead-letter consumer 프로세스는 위의 시나리오 중 하나라도 발생될 경우 주기적으로 재시도 합니다. 

### Policy Support

Quorum queue는 RabbitMQ 정책을 통해 구성할 수 있습니다. 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/20da7480-cc4a-445d-bbab-17199b02ecdb/Untitled.png)

### Use Cases

Quorum Queue는 의도적으로 설계되었습니다. 모든 문제에 사용되도록 설계되진 않았습니다.  

큐가 장기간 존재하고 시스템 작동의 특정 측면에 중요한 토폴로지에 사용되도록 의도된 용도이므로, 가능한 최소 지연 시간 및 고급 queue 기능보다 내결함성 및 데이터 안전성이 더 중요합니다.

[https://www.rabbitmq.com/quorum-queues.html](https://www.rabbitmq.com/quorum-queues.html)
