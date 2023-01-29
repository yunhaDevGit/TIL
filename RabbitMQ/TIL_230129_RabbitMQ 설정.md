**Queue Leader**

[https://www.rabbitmq.com/ha.html#leader-migration-data-locality](https://www.rabbitmq.com/ha.html#leader-migration-data-locality)

**Dead Lettering**

[https://blog.rabbitmq.com/posts/2022/03/at-least-once-dead-lettering/](https://blog.rabbitmq.com/posts/2022/03/at-least-once-dead-lettering/)

[https://minholee93.tistory.com/entry/RabbitMQ-Dead-Letter-Exchange-TTLTime-To-Live](https://minholee93.tistory.com/entry/RabbitMQ-Dead-Letter-Exchange-TTLTime-To-Live)

************Quorum Queue TTL************

[https://www.rabbitmq.com/ttl.html#queue-ttl](https://www.rabbitmq.com/ttl.html#queue-ttl)

******************************************************Quorum Queue Leader Locator******************************************************

[https://www.rabbitmq.com/quorum-queues.html#leader-placement](https://www.rabbitmq.com/quorum-queues.html#leader-placement)

---

[https://www.compose.com/articles/configuring-rabbitmq-exchanges-queues-and-bindings-part-2/](https://www.compose.com/articles/configuring-rabbitmq-exchanges-queues-and-bindings-part-2/)

- **TTL(Time-to-Live), 일명 TTL(x-message-ttl) :** 설정된 수명에 도달하고 아직 소비되지 않은 후 대기열의 메시지가 종료됩니다.
- **자동 만료(x-expires) :** 메시지에 액세스하지 않은 경우 일정 시간이 지나면 큐 자체가 만료됩니다.
- **최대 길이(x-max-length) :** 대기열이 보유할 수 있는 메시지 수를 정의합니다. 대기열 맨 앞의 메시지는 최대 길이에 도달했을 때 새 메시지가 들어갈 자리를 마련하기 위해 데드 레터링됩니다.
- **최대 길이 바이트(x-max-length-bytes) :** 대기열이 얼마나 커질 수 있는지를 바이트 단위로 정의합니다. 위의 최대 길이와 마찬가지로 대기열 맨 앞의 메시지가 먼저 데드 레터링됩니다.
- **데드 레터 교환, 일명 DLX(x-dead-letter-exchange) :** 데드 메시지가 수명 종료에 도달하여 최대 길이(메시지 또는 바이트) 대기열에 대해 구성되었거나 일부 문제로 인해 대기열에 의해 거부되었거나 소비자에 의해 nack(부정적 승인)되었으며 다시 대기열에 들어갈 예정이 아닙니다.
- **데드 레터 라우팅 키(x-dead-letter-routing-key) :** 데드 레터 교환에 새로운 라우팅 키 컨텍스트를 제공합니다. 데드 레터 라우팅 키가 설정되지 않은 경우 원래 라우팅 키가 사용됩니다.
- **최대 우선 순위(x-max-priority) :** 대기열이 사용자가 설정한 최대 수준까지 우선 순위 메시지의 우선 순위를 지정할 수 있습니다. 대기열의 최대값보다 높은 메시지는 이 최대 수준으로 다시 우선 순위가 지정됩니다.
