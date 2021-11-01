## Kafka Streams

Kafka Streams는 Kafka Cluster에 저장되는 데이터를 처리하고 분석하기 위한 클라이언트 라이브러리입니다.
Kafka는 대규모 메시지를 저장하고 빠르게 처리하기 위해 만들어진 제품이지만, 점점 뛰어난 성능에 일련의 연속된 메시지인 스트림을 처리하는 데에도 사용되기 시작했습니다. 이러한 Stream Processing 기능을 Kafka Streams API를 통해 제공합니다. 

### Stream Processing? Batch?
Stream Processing은 데이터들이 지속적으로 유입되고 나가는 과정에서 데이터에 대한 분석이나 질의를 수행하는 것을 의미합니다. **데이터가 도달하자마자 처리를 하기 때문에 Stream Processing은 실시간 분석**이라고 불리기도 합니다. 
기존에는 대규모 데이터를 처리하기 위해 Batch를 사용하곤 했는데, Batch는 **이미 저장된 데이터를 기반으로 분석이나 질의를 수행하고 특정 시간에 처리하는 특징**을 가집니다. 하지만 Stream Processing은 데이터가 이동 중이거나, 생성되어 수신 되는 즉시 처리하기 때문에 실시간 처리를 합니다. 
실시간으로 데이터를 분석하고 이벤트 중심으로 데이터를 처리하기 때문에 개발자가 빅데이터 처리에 신경 써야 할 많은 부분들을 손쉽게 해결해줍니다. 

### Kafka Streams

Kafka Streams는 Kafka Producer와  Consumer 클라이언트 기반으로 구축되었습니다. 때문에 일반 클라이언트보다 훨씬 강력하고 표현력이 뛰어납니다. 
위에서도 말했 듯이, Consumer가 단순히 메시지를 받은 후 처리하고 끝난다면, Kafka Streams는 메시지를 받아 연속된 처리를 위한 파이프라인을 구성할 수 있습니다. 
Kafka Streams는 카프카 내부에서 메시지 파이프라인을 쉽게 구성할 수 있도록 해줍니다. 즉, 내부 토픽을 이용하여 파이프라인을 구성할 수 있습니다. 

![](https://blog.kakaocdn.net/dn/uF8L1/btqEmp9yJ41/RSrS24sAhlRy1KxLpl8431/img.jpg)


### Kafka Streams 특징
- 간단하고 가벼운 클라이언트 라이브러리이므로 기존 자바 애플리케이션에서 쉽게 사용할 수 있습니다.
- 프레임워크에 종속되지 않고, 라이브러리 의존성을 추가하여 몇 가지 설정만 해주면 알아서 파이프라인이 구성됩니다.
- 내부 메시징 계층으로서 시스템이나 카프카에 대한 의존성이 없습니다.
- Kafka Broker나 Client에 장애가 생기더라도 스트림에 대해선 1번만 처리되는 것을 보장합니다.
- fault-tolerant 로컬 상태 저장소를 지원하므로 빠르고 효율적인 저장 작업이 가능합니다.


## Kafka Streams 주요 개념
### Kafka Processing Topology
- **Stream** : Kafka Streams에서 제공하는 가장 중요한 추상화로, Kafka Streams API를 사용해 생성된 토폴로지로 **끊임없이 전달되는 데이터 세트**를 의미합니다.  key-value 형태로 기록됩니다.
- **Stream Processing Application** : **kafka stream client를 사용하는 애플리케이션**으로 하나 이상의 토폴로지를 처리하는 로직을 의미합니다.
- **Stream Processor** : 프로세서 토폴로지를 이루는 하나의 노드를 의미합니다. 여기서 노드는 연결된 하나의 입력 스트림으로부터 데이터를 받아서 변환한 다음 다시 연결된 프로세서에게 보내는 역할을 합니다. 
- **Source Processor** : 위쪽으로 연결된 프로세서가 없는 프로세서
- **Sink Processor ** : 토폴로지 아래쪽에서 프로세서 연결이 없는 프로세서. 

![](https://kafka.apache.org/30/images/streams-architecture-topology.jpg)

