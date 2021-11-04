# Kafka Message, Topic, Partition

### Kafka Message
카프카 메시지는 Key와 Value로 구성됩니다. 

key는 해당 메시지가 카프카 브로커 내부에 저장될 때 저장되는 위치에 관련된 요소입니다. 
프로듀서가 메시지를 브로커로 전달할 때, 프로듀서 내부의 파티셔너(Partitioner)가 저장 위치를 결정하는데, 이때 key의 값을 이용하여 연산하고 그 결과에 따라 저장되는 위치를 결정합니다. 

value는 메시지가 전달하고자 하는 내용입니다. 단순히 문자열이 될 수도 있고, JSON이나 특정 객체가 될 수도 있습니다. 
+) 다양한 타입의 값을 보낼 수 있는 것은 브로커를 통해 메시지가 발행되거나 소비될 때, **메시지 전체가 직렬화/역직렬화 되기** 때문입니다. 


## Topic
**Topic은 메시지를 구분하는 논리적인 단위**로, 동일한 메시지들은 논리적으로 같은 문맥(Context)를 가집니다. 
예를 들어, 주문에 관한 내용을 담고 있는 메시지를 발행하고, 소비하기 위해서는 우리는 order라는 토픽을 생성하고 이 토픽을 기준으로 메시지를 발행, 소비할 수 있습니다. 


## Partition
논리적인 단위의 카프카 토픽을 기준으로 발행되는 메시지들은 **브로커 내부의 물리적인 단위인 파티션으로 나뉩니다.** 모든 토픽은 각각 대응하는 하나 이상의 파티션이 브로커에 구성되고, 발행되는 토픽 메시지들은 파티션들에 나뉘어 저장됩니다. 

**broker 내 partition 배치**
Kafka에서는 각 broker의 리소스를 최대한 활용하기 위해 각 Topic들을 균등하게 broker에게 분산하도록 설계되어 있습니다. 그래서 각 파티션들은 broker들에게 비교적 균등하게 분산되어 있습니다. 만약 broker들에게 분산되어 있는 파티션의 위치가 맘에 들지 않으면 관리자가 수동으로 조정할 수 있습니다. 

https://www.popit.kr/kafka-%EC%9A%B4%EC%98%81%EC%9E%90%EA%B0%80-%EB%A7%90%ED%95%98%EB%8A%94-tip/



### Replication
kafka에서는 높은 가용성을 위하여 **Replication** 을 제공합니다. 
Replication은 각 Topic의 Partition들을 Kafka Cluster 내의 다른 Broker들로 복제하는 것을 말하며 Topic 생성 시 Replication 수를 지정할 수 있습니다. 
생성된 Replication은 Leader와 Follower로 나뉘며 ISR(In Sync Replica)라는 일종의 Replication Group을 형성하여 관리합니다. 

**Replication Factor**
Replication Factor는 Topic 단위로 설정할 수 있습니다. 쉽게 말해, 얼마만큼 partition을 복사할 것인지 설정하는 것이다.
기본값은 1로 설정되어 있고, 이를 변경하고 싶으면 kafka 설정 파일에서 **default.replication.factor** 값을 변경하면 됩니다.
Topic 단위로 replication factor를 설정한다 해서 **Topic을 replication하는 것이 아닌, Topic을 이루는 각각의 partition을 replication하는 것입니다.**

예를 통해 자세히 알아보겠습니다. 

아래와 같이 설정해주도록 하겠습니다.

> Topic : test (1)
> partition 개수 : 3
> replication factor : 1

test라는 이름의 Topic이 생성 되고, replication factor 값을 1로 해주었으니, 아래 그림처럼 3개의 partition이 복제 없이 여러 Broker에 분산된 형태로 구성됩니다. 
![](https://blog.kakaocdn.net/dn/SZHUV/btrbJH9vkfP/eQbWnneLdCy8Jm06J0mgyK/img.jpg)


만약 replication factor가 3인 Topic을 생성한다면 아래와 같이 3개의 broker에 복제됩니다.
![](https://blog.kakaocdn.net/dn/bmmd1R/btrbAIa4n1Z/NMUREC2B5Ggkgwc19CUQFK/img.png)

replication factor 값을 높이면 가용성이 증가하는 것은 분명하지만, 그에 비례하여 디스크 사용량이 배가 되며 Broker의 일부 리소스 사용량 또한 증가하게 됩니다. 따라서 replication factor 값을 무작정 높게 설정하기 보다는 Topic에 저장되는 Data의 중요도에 따라 적절히 값을 조정하여 운영하는 것이 바람직합니다. 

**Leader & Follower**
Topic은 1개 이상의 Partition으로 구성되는데, 1개의 Leader 파티션과 0개 이상의 Follower 파티션으로 구성됩니다. 

예를 들어 Replication factor이 1이라면 partition은 1개의 leader partition과 0개의 follwer partition으로 이루어집니다. 
![](https://blog.kakaocdn.net/dn/bxDioq/btrb6TDpgQL/OCTEovQwsrfvdKeSIb5QxK/img.png)


만약 Replication factor가 3이라면 1개의 leader partition과 2개의 follower pratition으로 이루어집니다.

![](https://blog.kakaocdn.net/dn/bSKNBA/btrcf68x6Ga/ymPB0lJSJmIY9kJdDEmdaK/img.png)

Broker로 들어오는 모든 메시지는 Leader Partition에서만 **read/write** 할  수 있습니다.

*topic으로 통하는 모든 데이터의 read/write는 오직 leader와 이루어집니다*

반대로 Follower Partition에서는 Leader Partition에 쓰인 메시지들을 그대로 복제하여 Leader와 동일하게 유지합니다. 
만약 Leader Partition이 중단되거나 장애가 발생할 경우, Follower Partition 중 하나가 새로운 Leader Partition으로 선출됩니다.  이때, Follower Partition들 중 Leader Partition의 데이터를 제대로 복제해오지 못한다면 해당 Partition을 제외한 나머지 Follower Partition들 중 Leader Partition을 선출해야 하는데, 이를 위해 ISR(In Sync Replica)라는 개념이 도입되었습니다.

**ISR(In Sync Replica)**
ISR에 대해 좀 더 살펴보겠습니다. replication group이라고 생각하시면 좀 쉽습니다.
예제를 보면 좀 더 이해하기 쉬우니 빠르게 예제로 시작해보겠습니다. 

아래와 같이 partition 개수와 replication factor를 각각 3으로 설정한 Topic을 생성했다고 하겠습니다. 

아래  그림처럼 각 Topic에 Leader와 Follower Partition이 만들어질 겁니다. 
이렇게 만들어진 파티션들 중 Leader Partition과 제대로 **동기화** 가 이루어진 Follower Partition은 ISR이라는 Replication Group으로 구성됩니다.

![](https://lh5.googleusercontent.com/iUYWkN8zfKHAoKhL2ZQZ-45l9SDtQ0CJ-lrrb7Yr4L3SxfJQdoP0VZIHdhVPvp4BIAn6CmQ-CGnejBzsaTiLf2vAcpdgvtas_sc94OYHEs0Q9hpZzAeWgwKPA5FNAua20yiayBrRyq_3)


동작하던 중, Broker 4에 있는 Partition2 Follower Partition이 Leader Partition의 데이터를 정상적으로 못 받아올 경우, 아래와 같이 ISR에서 해당 Follower Partition을 제외시킵니다. 
제외 된 Partition은 나중에 Leader Partition이 죽더라도 새로운 Leader Partition으로 선출될 수 없게 됩니다. 

*즉, Leader Partition은 ISR에 포함된 Follower Partition들 중에서 선출됩니다. 다시 말해, ISR에 포함된 Follower Partition들은 언제든지 Leader가 될 수 있습니다. *

![](https://lh5.googleusercontent.com/fpL6gFlgxTgywXRbEZE6z0Bksnzx9iTEf5sK_OY6VktnS78ORowa6PvvrluBOrUwr2Cka3Qemw5kdzKyBH-6YyOXNUeTmzyI_CQtOpd_uFfNHEM3ELVNj0xNx4_-jE9ED1ZlaH3M4F6M)

아래 그림처럼 Broker1에 장애가 발생하여 동작하지 못한다고 가정해보겠습다.
Broker1이 down 되면서 leader partition1 과 follower partition3에 변화가 생깁니다.
leader partition 1이 down 되었으므로 IRS에 속한 follower들은 모두 leader가 될 수 있다는 조건에 따라, follower partition 중 하나가 새로운leader partition이 됩니다.
partition 3의 경우 follower partition이 down 되었기 때문에 기존에 있던 leader partition이 계속해서 read/write를 하게 됩니다.

![](https://lh3.googleusercontent.com/N2sUwWkXJHIa-qJimH4Gp1stls0mQ_ut9gAXwG9wYGJ8vnnUT_Lu8fviPn8bldDAOkFKG4sqmoiqCcf6UznX7dpj597QzP3aFjHMFI4ve5XXBb295xkLv7eazsJt8y84A5ESebpHOBzF)





https://damdam-kim.tistory.com/17
https://www.popit.kr/kafka-%EC%9A%B4%EC%98%81%EC%9E%90%EA%B0%80-%EB%A7%90%ED%95%98%EB%8A%94-topic-replication/
