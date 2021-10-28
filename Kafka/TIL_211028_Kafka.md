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

