### Event Streaming
event streaming은 데이터 베이스나 모바일 기기, 클라우드 서비스와 같은 이벤트 소스에서 event stream 형태로 실시간으로 데이터를 캡쳐 하는 방법입니다.  또한 이 event stream은 나중에 검색할 수 있도록 영구적으로 저장합니다. 실시간으로 event stream에 대한 조작 및 처리, 대응합니다. 또한 event stream을 필요에 따라 각각 다른 목적지로 라우팅 해줍니다. 
Event streaming은 데이터의 지속적인 흐름과 해석을 보장하여 올바른 정보가 적절한 시간에 올바른 위치에 있도록 합니다. 


### Event Streaming의 사용
- 증권 거래소, 은행, 보험 등의 실시간 결제와 금융 거래 처리를 합니다.
- 물류 및 자동차 산업과 같이 자동차, 트럭, 배 등을 실시간으로 추적하고 모니터링 합니다.
- IoT나 기타 장비의 센서 데이터를 지속적으로 캡처하고 분석합니다. 
- 소매, 호텔 및 모바일 애플리케이션과 같은 고객 상호 작용 및 주문을 수집하고 즉시 대응합니다. 
- 회사의 다른 부서에서 생성된 데이터를 연결, 저장 및 사용 가능하게 만듭니다. 
- 데이터 플랫폼, 이벤트 중심 아키텍처 및 마이크로 서비스의 기반 역할을 합니다. 
- 병원에서 치료 중인 환자를 모니터링하고 상태 변화를 예측하여 응급 상황에서 시기 적절한 치료를 보장합니다



### Apache Kafka? event streaming platform?
1. 다른 시스템에서 데이터를 지속적으로 가져오기/내보내기, event stream을 publish(write) 및 subscribe(read) 합니다. 
2. 언제든 사용할 수 있도록 event stream을 저장합니다. 
3. event stream이 발생할 때 event stream을 처리합니다. 

위의 모든 기능은 분산 되고 확장성이 뛰어나며, 탄력적이며 안전한 방식으로 제공됩니다. 



### Kafka 동작에 대한 간략한 소개
Kafka는 고성능 TCP로 통신하는 server와 client로 구성된 분산 시스템입니다. 
Kafka는 클라우드 환경에서 bare-metal 하드웨어, 가상 머신, 컨테이너에 배포될 수 있습니다. 

**Servers** : Kafka는 클라우드 공간 또는 여러 데이터 센터에서 하나 이상의 서버 클러스터로 동작합니다. 이러한 서버 중 일부는 브로커라고 하는 스토리지 계층을 형성합니다. 다른 서버는 Kafka Connect를 실행하여 지속적으로 event steam을 import, export 하고, kafka를 관계형 데이터베이스 및 기타 Kafka 클러스터와 같은 기존 시스템과 통합합니다. 
만약 서버가 실패하여 동작하지 않을 경우, 다른 서버가 중지된 서버의 작업을 가지고 가서 대신 작업을 수행합니다. 이로 인해 데이터 손실 없이 지속적인 운영을 보장할 수 있습니다. 


**Clients** : 네트워크 문제 또는 시스템 장애 발생 시에도 분산 애플리케이션 및 마이크로 서비스 규모에 맞게 event stream을 병렬로 읽고, 쓰고, 처리할 수 있습니다. clients는 수준 높은 kafka stream 라이브러리와 Java 및 Scala, Go, Python 등 기타 여러 프로그래밍 언어로 REST API를 사용할 수 있습니다. 




### 메인 컨셉과 용어

**event**
- "어떤 일이 발생했다"라는 사실. message라고도 부릅니다. 
- 만약 kafka에 데이터를 쓰거나 읽을 때, 이벤트 형식으로 이 작업을 수행합니다.
- event는 key, value, timestamp와 optional metadata headers를 가지고 있습니다.

**producers** 
- kafka에 이벤트를 publish(write) 하는 클라이언트 애플리케이션입니다. 

**consumers**
- 이벤트 구독 및 읽고 처리하는 애플리케이션입니다. 

*producer와 consumer는 완전히 분리되어 있으며, Kafka의 높은 확장성을 위한 필수 요소입니다.*
- 예를 들어 producer는 consumer를 기다릴 필요가 없습니다. 

Kafka는 event를 단 한번만 처리하는 기능과 같은 다양한 보장을 제공합니다. 

**topics**
- event가 구성되고  저장되는 곳입니다.
- 쉽게 topic을 파일 시스템의 폴더라고 생각하면 됩니다. 또한 event가 폴더 안의 파일이라고 생각하면 됩니다. 
- 내결함성 및 고가용성을 위해 모든 topic은 replicate 구성을 할 수 있습니다. 


기존 전통적인 메시징 시스템과 달리 events는 소비된 후에 삭제되지 않고, topic별 설정을 통해 kafka가 이벤트를 유지 시켜야 하는 기간을 설정 후 해당 기간이 지난 event를 삭제합니다. 

**partition**
- topic은 다른 Kafka broker에 있는 여러 'buckets'에 분산 되어 있습니다. 
- 이는 client 애플리케이션이 여러 brokers에서 동시에 데이터를 읽고 쓸 수 있도록 하기 때문에 확장성에 매우 중요합니다. 
- 실제로 topic에 새로운 이벤트가 발생하면 topic의 파티션 중 하나에 추가됩니다.
- 동일한 키를 가진 이벤트는 동일한 파티션에 저장되고, Kafka는 파티션에 쓰여진 순서대로 소비할 수 있도록 보장합니다. 

![](https://kafka.apache.org/images/streams-and-tables-p1_p4.png)


## Kafka의 사용

Kafka의 대표적인 기능은 메시지 큐/로그 수집/ETL 도구 등이 있다.

이전부터 해당 기능을 제공하던 제품이 있지만, 이전 제품에서 처리하지 못할 정도의 대용량 데이터를 처리해야 할 때 사용할 수 있

### Messaging
kafka는 전통적인 메세지 브로커들을 대체하는 역할을 합니다. 

대부분의 메세징 시스템과 비교했을 때, kafka가 처리량이 더 우수하고, replication, 기본 제공되는 파티셔닝 등 대규모 메세지 처리를 하는 응용 프로그램에 적합한 솔루션입니다. 

### Website Activity Tracking
kafka의 원래 사용 목적은 실시간 pub-sub feed 구성으로 사용자의 활동을 추적하는 파이프라인을 재구축 할 수 있었습니다. 

즉, 사이트 활동이 활동 유형당 하나의 topic으로 publishing 됩니다. 

이는 실시간 모니터링, 실시간 처리 등 다양한 곳에 사용될 수 있습니다. 


하지만 활동 추적은 각 사용자 페이지에서 수많은 활동 메시지를 생성하기 때문에 볼륨이 클 수 있습니다. 


### Metrics
Kafka는 종종 운영 모니터링 데이터에도 사용됩니다. 

이는 분산 애플리케이션의 통계를 집계하여 운영 데이터의 중앙 집중식 피드 생성을 포함합니다. 


### Log Aggregation
Kafka는 log 집계 솔루션을 대안으로도 사용됩니다. log aggregation은 일반적으로 서버의 물리 로그 파일들을 수집하고 중앙에 배치합니다. Kafka는 파일의 세부사항을 추상화 하고, 로그 또는 이벤트 데이터를 메세지 스트림으로 추상화합니다. 

이를 통해 처리 지연이 단축되고 여러 데이터 소스 및 분산 데이터 소비를 보다 쉽게 지원할 수 있습니다. Scribe나 Flume과 같은 로그 중심 시스템과 비교했을 때, kafka는 똑같이 우수한 성능, 복제로 인한 더 강력한 유지성, 훨씬 더 낮은 종단간 대기 시간을 제공합니다. 


### Stream Processing
여러 단계로 구성된 처리 파이프라인에서 데이터를 처리합니다. 여기서 원시 입력 데이터는 Kafka 주제에서 소비된 다음 추가 소비 또는 후속 처리를 위해 집계, 강화 또는 새로운 주제로 변환됩니다. 예를 들어 뉴스 기사를 추천하기 위한 처리 파이프라인은 RSS 피드에서 기사 콘텐츠를 크롤링하여 "기사" 주제에 게시할 수 있습니다. 추가 처리를 통해 이 콘텐츠를 정규화하거나 중복 제거하고 정리된 기사 콘텐츠를 새 주제에 게시할 수 있습니다. 최종 처리 단계에서 이 콘텐츠를 사용자에게 추천할 수 있습니다. 이러한 처리 파이프라인은 개별 주제를 기반으로 하는 실시간 데이터 흐름의 그래프를 생성합니다.\

### Event Sourcing
 상태가 바뀐 시간 순으로 로깅 된 레코드 시퀀스의 디자인 스타일이다. 
 
 Kafka는 매우 큰 로그 데이터 저장소를 지원하고, 이는 애플리케이션의 훌륭한 백엔드가 됩니다.

### Commit Log
Kafka는 분산 시스템에 대한 외부 커밋 로그 역할을 할 수 있습니다. 로그는 노드 간의 데이터 복제를 돕고, 실패한 노드가 데이터를 복구할 수 있는 매커니즘 역할을 합니다. 


## Ecosystem
기본 배포판 외에 Kafka와 연동되는 많은 tool이 있습니다. 


## APIS

### Producer API
- Producer API는 Kafka cluster에 있는 topic에 데이터 스트림을 전송하는 API를 제공합니다. 


### Consumer API
- Consumer API는 Kafka cluster의 topic에 있는 데이터 스트림을 읽어오는 API를 제공합니다. 


### Stream API
- Stream API는 데이터 스트림을 input topic에서 output topic으로 변환하는 API를 제공합니다. 
- Kafka Stream은 입력 및 출력 데이터가 Kafka 클러스터에 저장되는 애플리케이션 및 마이크로서비스를 구축하기 위한 클라이언트 라이브러리입니다. 


### Connect API
- Connect API는 일부 데이터 시스템에서 Kafka로 지속적으로 가져오거나 Kafka에서 일부 연결된 데이터 시스템에 push 하는 connector 구현하는 API를 제공합니다. 
- 사용자들은 이 API를 직접 사용할 필요 없이 미리 빌드 된 connector를 사용할 수 있습니다. 


### Admin API
- Admin API는 topic, broker, acl과 다른 kafka 객체들을 관리 및 검사하는 API를 제공합니다. 

