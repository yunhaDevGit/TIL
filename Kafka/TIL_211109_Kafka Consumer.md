# Kakfa Consumer

컨슈머는 보통 카프카 컨슈머 API와 그것으로 구성된 애플리케이션을 말합니다. 
일반적으로 컨슈머가 토픽을 구독(Subscribe) 혹은 읽는다(Read)고 하는데, 이는 컨슈머가 토픽 파티션에 저장된 메시지들을 가져오는 것을 말합니다. 
컨슈머의 3가지 특징은 다음과 같습니다. 
- Polling 구조
- 단일 토픽의 멀티 커슈밍
- 컨슈머 그룹

### Polling 구조
일반적으로 다른 메시징 큐는 메시지 큐에서 메시지를 push하는데, 카프카는 컨슈머가 브로커로부터 메시지를 pull 해옵니다. 
push 방식의 가장 큰 단점은 메시지 큐가 컨슈머 측의 성능도 염두해야 합니다. 즉, 메시지 큐가 컨슈머로 메시지를 push 할 때, '이 정도는 할 수 있겠지'라고 컨슈머의 환경을 고려해야 합니다.
하지만 컨슈머가 브로커로부터 메시지를 요청하는 polling 구조는, **컨슈머가 자신이 원하는 만큼 브로커로 메시지를 요청합니다. ** 이러한 구조의 가장 큰 장점은 각 컨슈머가 자신의 환경에 메시지 구독 성능을 최적화 할 수 있다는 것입니다. 추가로 브로커는 컨슈머가 요청하는 만큼 메시지를 전달하면 되기 때문에 더 이상 컨슈머의 환경을 고려할 필요가 없습니다. 


### 단일 토픽의 멀티 컨슈밍
![](https://blog.kakaocdn.net/dn/b9eWpo/btqDlj3DA5O/z16PGBflaS8bJyg9dMnbV0/img.png)

카프카 컨슈밍의 또 다른 특징 중 하나는 **하나의 토픽에 서로 다른 컨슈머 애플리케이션이 동시에 구독할 수 있다는 것입니다. ** 이렇게 단일 토픽에 대해 멀티 컨슈밍이 가능한 이유는 컨슈머가 메시지를 읽을 때 브로커의 메시지가 삭제되는 것이 아니기 때문입니다. 대신 **각 컨슈머가 어느 토픽 파티션의 어느 오프셋까지 읽어갔는 지를 컨슈머 오프셋이라는 토픽에 저장합니다.**
컨슈머 오프셋을 통해 **컨슈머 애플리케이션이 중단되었다가 다시 시작될 경우 자신이 어디서부터 메시지를 읽어야 하는지 알 수 있습니다.** 즉, 컨슈머 상태와 관계없이 안정적인 메시지 구독이 가능해집니다.


### 컨슈머 그룹
브로커는 성능을 위해 하나의 토픽을 여러 파티션으로 병렬 구성하여 처리합니다. 하지만 둘 이상의 파티션을 하나의 컨슈머로만 처리한다면 성능상의 문제가 발생할 수 있습니다. 
그래서 카프카 컨슈머는 **하나 이상의 컨슈머가 컨슈머 그룹을 구성하여 토픽을 구독할 수 있습니다.**
![](https://blog.kakaocdn.net/dn/bWRQzO/btqDmnYJpx4/cJmkH1kfJoGm2FrFrMk7O1/img.png)


## 컨슈머 동작 원리

![](https://blog.kakaocdn.net/dn/cuLxkJ/btrb6TpGA8W/rMNm7d7ThbVdBJ8t9cvlkK/img.png)
Kafka Consumer 구조

|구성요소| 역할 |
|--|--|
| ConsumerNetworkClient | kafka consumer의 모든 Network 통신을 담당 |
| SubscriptionState | Topic/Parition/Offset 정보를 저장하고 관리하는 담당 |
| ConsumerController | Consumer Rebalance / Offset 초기화 및 커밋 담당 |
| HeartBeatThread | 백그라운드에서 동작하며, Consumer가 살아 있다고 Coordinator에게 알려주는 담당 |
| Fetcher | 브로커로부터 데이터를 가져오는 담당 |


**ConsumerCoordinator**
ConsuemerCoordinator는 컨슈머 rebalance, offset 초기화, offset 커밋을 담당합니다.
ConsumerCoordinator 내부에 heartbeat 스레드가 존재해 주기적으로 heartbeat를 GroupCoordinator에게 전송합니다.


**컨슈머 rebalance**
1. GroupCoordinator 찾기
	- FindCoordinator API를 사용하여 JoinGroup 요청을 보낼 GroupCoordinator를 찾습니다.
2. Join
	- KafkaConsumer들이 JoinGroup API를 사용하여 GroupCoordinator에게 그룹 참여 요청을 보냅니다.
	- GroupCoordinator는 그룹에 참여하는 클라이언트 정보와 그룹 메타데이터를 수집합니다.
	- **Group의 정보와 Subscription 정보를 수집 후 리더를 선출합니다.**
	- GroupCoordinator에 의해 리더로 선정된 클라이언트가 그룹 내 파티션을 할당합니다.
3. Sync
	- 리더가 그룹 내 Consumer에게 partition 할당 후, Group Coordinator에게 해당 정보를 보냅니다.


### Fetcher
쉽게 말하자면 kafka topic에 있는 데이터들을 가져오는 것입니다.

![](https://blog.kakaocdn.net/dn/bQ15su/btrb8262vHC/wMEnmUuKnx3K84kvs1KNW0/img.png)

max.partition.fetch.bytes와 fetch.min.bytes 설정을 통해 consumer가 얼만큼의 데이터를 가져올 것인지 설정하여 데이터를 가져오게 됩니다.
fetch.min.bytes의 기본 설정은 1byte며 max.partition.fetch.bytes의 기본 설정은 1mebibytes입니다.

또한 max.poll.records 설정을 통해 데이터를 가져오는 Record 개수를 설정할 수 있습니다. (default : 500개)

신뢰성 있고 주기적인 polling을 하기 위해서는 ***max.poll.records를 1로 하면 됩니다.***
-> 만약 polling 하는 데이터 프로세싱 과정에서 로직 문제로 지연이 발생한다면 poll.interval도 길어지기 때문에 **record 수만큼 지연시간이 늘어납니다.** 그러면 애플리케이션은 정상적으로 살아 있어도, Consumer가 제대로 consuming 하지 못해 Kafka Record가 consuming 안되는 장애가 발생할 수 있습니다. 

### HeartBeatThread
HeartBeatThread는 background에서 동작하며 Coordinator에게 살아있음을 알립니다.

기존에는 Kafka Consumer가 데이터 프로세싱 하는 것을 기반으로 살아있고, 죽었음을 판단했었는데
**데이터 프로세싱과 Health Check를 같이 하니, 데이터 프로세싱이 길어지면 Consumer가 살았는지 죽었는지 즉각 확인할 수 없는 이슈** 때문에 별도의 Thread를 유지하게 되었습니다.

핵심은 ***데이터 프로세싱 부분과  Consumer 생존 여부를 알리는 부분을 분리*** 한 것입니다.


### Rebalancing
HeartBeat를 토대로 Consumer 생존(?) 여부에 대해 판단 후 Consumer 개수가 변하면 Rebalancing이 이뤄집니다.
또한 Topic에 변경 사항이 생긴다면 마찬가지로 Rebalancing이 이뤄집니다.


## Consumer Config

- group.id 
	- 컨슈머 그룹을 식별하는 고유 id입니다. 메시지를 전송할 때 지정하는 topic 이름과 다릅니다.
	- zookeeper에서는 각 그룹의 메시지 offset을 관리하는데 그룹 id가 같으면 offset 값 또한 공유됩니다.

- bootstrap.servers
	- 프로듀서와 동일합니다.
	- 연결할 서버 정보입니다.

- fetch.min.bytes
	- 한 번에 가져올 수 있는 최소한의 데이터 크기입니다.
	- 기본값인 1의 경우 즉시 가져오는 것을 뜻하며 1보다 크거나 데이터가 설정한 값보다 작은 경우에는 요청을 처리하지 않고 대기합니다.

- auto.offset.reset
	- 카프카의 초기 offset이 없거나 데이터가 삭제하여 현재 존재하지 않는 경우 아래의 설정을 따릅니다.
		- earliest : 가장 빠른 오프셋으로 자동 재설정
		- latest(default) : 최신 오프셋으로 자동 재설정
		- none : 이전 오프셋이 발견되지 않으면 컨슈머 그룹에 예외 던짐
		- anything else : 컨슈머에게 예외 던짐

- session.timeout.ms
	- 해당 시간동안 HeartBeat이 도착하지 않으면 Group Coordinator는 해당 Consumer를 Group에서 제외 시킵니다.
	- default = 10000ms = 10초

- heartbeat.interval.ms
	- 해당 주기동안 HeartBeat를 Group Coordinator로 전송합니다.
	- 일반적으로 session.timeout.ms의 1/3 으로 합니다. 반드시 session.timeout.ms보다 작아야 합니다.
	- default = 3000ms = 3초

- max.poll.interval.ms
	- poll 메서드가 호출되어야 하는 시간입니다. 만일, 해당 시간 내에 poll이 호출되지 않으면 group에서 제외됩니다. 
	- 살아있다고 신호를 보내지만 실제로 메시지를 소비하지 않는 경우, 해당 컨슈머는 실패 된 것으로 간주되고, 컨슈머 그룹은 다른 구성원에게 파티션을 재할당하기 위해 재조정 됩니다. 
	- HeartBeat 쓰레드가 poll 호출 간격을 측정하게 됩니다.

- partition.assignment.strategy
	- 파티션 할당 전략을 설정합니다.
	- default : RangeAssignor
		- 파티션을 숫자 순서로 놓고, 컨슈머는 사전순으로 놓습니다. 그런 후 각 컨슈머에 할당할 파티션의 숫자를 결정하기 위해 파티션의 숫자를 전체 컨슈머 숫자로 나누고 남는 파티션은 첫 번째 컨슈머가 가집니다.



https://kafka.apache.org/documentation/#consumerconfigs
https://huisam.tistory.com/entry/kafka-rebalancing
https://always-kimkim.tistory.com/entry/kafka101-consumer?category=876258
