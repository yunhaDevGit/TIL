
# Kafka
Kafka는 여러 대의 분산 서버에서 대량의 데이터를 처리하는 분산 메시징 시스템이다 \
메시지를 받고, 받은 메시지를 다른 시스템이나 장치에 보내기 위해 사용한다

### Kafka 사용 이유
Kafka는 대량의 데이터를 **높은 처리량**과  **실시간**으로 다룰 수 있는 제품

- 확장성 : **여러 서버로 확장 구성** 할 수 있기 때문에 데이터의 양에 따라 시스템 확장이 가능
- 영속성 : **수신한 데이터를 디스크에 유지**할 수 있기 때문에 언제든지 데이터를 읽을 수 있다
- 유영성 : **연계할 수 있는 제품이 많기** 때문에 제품이나 시스템을 연결하는 허브 역할을 할 수 있다
- 신뢰성 : **메시지 전달 보증**을 하므로 데이터 분실 걱정을 하지 않아도 된다

```java
**높은 처리량과 안정성을 제공하기 위한 기능**
1. 페이지 캐시
	- OS의 페이지 캐시를 활용하는 방식으로 설계되어 있다. 
	  (페이지 캐시는 직접 디스크에 읽고 쓰는 대신 물리 메모리 중 애플리케이션이 사용하지 않는 일부 잔여 메모리를 활용)

2. 배치 전송 처리
	- 메시지를 묶어서 처리하여 단건으로 통신할 때보다 네트워크 오버헤드를 줄이고, 장기적으로는 더 빠르고 효율적으로 처리 가능하게 함
		
3. 압축 전송
	- 지원하는 압축 타입 : gzip, snappy, lz4, zstd 등

4. 토픽, 파티션, 오프셋
	- 토픽이라는 곳에 데이터를 저장하는데 여러 파티션으로 나눠 병렬 처리를 통해 높은 처리량을 수행

5. 고가용성 보장
	- 분산 시스템이므로 하나의 서버나 노드가 다운되어도 다른 서버 또는 노드가 장애가 발생한 서버의 역할을 대신해 안정적인 서비스 가능
		(파티션에 리더와 팔로워가 있어 팔로워는 리더의 데이터를 주기적으로 복제한다)
		(이상적인 리플리케이션 팩터 수를 3으로 구성하라고 한다)

```

### Kafka 목표
1. 높은 처리량으로 실시간 처리
2. 임의의 타이밍에서 데이터 소비
3. 다양한 제품과 시스템에 쉽게 연동 가능
4. 메시지를 전달 보증


### Kafka 개요
kafka는 위의 목표를 실현시키기 위해 아래와 같이 구성되었습니다.

**메시징 모델과 스케일 아웃**

![](https://lh4.googleusercontent.com/DuQrN-a723fhSj6R2-0CaIyYvSSNvd2QcsyOf80Sv2xLNsMIjNFUO4wTJoNARCPgiXqI4_FENH8yaTVDrNJv4ogbDnsLGy1VhKMVR7Evtv0gxZNx_3w_0n8RyE75PtEsk62Vd5TgtzI)

- Kafka 메시징 모델은 높은 처리량과 실시간 처리를 위해  '큐잉 모델'과 'publish/subscribe(펍/섭) 메시징 모델'  이 두가지 특징을 모두 가진 형태로 만들어졌습니다. 

		**![](https://lh3.googleusercontent.com/TbsodvdH_TpBOHjxImSaO2ZxVKJrNCYKm7N4iH72PfOi5kXdS9Fyvki1ByhK-EQc_SQrdTJXS5RquFnjKAXfVrzELH9M_nnfWqCWwdNo1do9E0f4WM55ZK6PFcjXJcWqNOix7Bfgzlc)**
	```
	큐잉(queuing) 모델
	브로커(Broker) 안에 큐가 있습니다. Producer는 큐에 메시지를 보내고, Consumer가 큐에서 메시지를 추출합니다. 
	하나의 큐에 대해 여러 Consumer가 접근할 수 있어 병렬 처리를 할 수 있습니다. 
	Consumer가 메시지를 받으면 다른 Consumer는 메시지를 받을 수 없습니다.
	(즉, 하나의 메시지는 하나의 컨슈머에서만 처리됩니다)
	```
![](https://lh4.googleusercontent.com/31lgML9S_01PgyuhfCaXWAr6ax0yo9epbVZLXkEt_5ChX7pRKbTauNV35GCtpssvcmjGLi0AyUCPF7_vPQ7sGPBkS24XiVYaw3FYhg3g4DdaoompZ9LiLZ8Q6sDas7nnCYInT1PXI9w)
	
	```
	펍/섭 메시징 모델
	Publisher = Producer(메시지 생산자)
	Subscriber = Consumer(메시지 소비자)
	Publisher가 Subscriber에게 바로 메시지를 전달하는 것이 아닌 Broker를 통해 전달합니다.
	Publisher는 누가 메시지를 수신하는지 알 수 없고, Broker 안의 Topic에 메시지를 등록합니다. 
	Subscriber는 여러 개의 Topic 중 하나를 선택하여 메시지를 받습니다. 만일 여러 Subscriber가 동일한 Topic을 구독했을 경우 모두 동일한 메시지를 받습니다.
	```

**디스크로의 데이터 영속화**
- 데이터 영속화를 해주는 메시지 큐도 있지만, 실시간 접속에만 중점을 두며 장기적인 보존을 지원하지 않는 경우가 많습니다. 
- 반면 카프카는 영속화를 지원합니다. 배치 처리의 경우 데이터를 일정 기간 유지해야 하는데 이런 데이터들을 모두 메모리에 올려 두는 것이 용량적인 측면에서 불가능하므로, 디스크에서 이뤄집니다. 

**이해하기 쉬운 API 제공**
- 다양한 제품과 시스템에 쉽게 연동 될 수 있도록 이해하기 쉬운 API를 제공합니다. 
- Producer와 Consumer를 쉽게 접속할 수 있도록 'Connect API'를 제공합니다. 각각 이 API를 사용하여 각종  외부 시스템과 접속합니다. 
- 또한 API 기반으로 카프카에 접근하기 위한 프레임워크로 Kafka Connect도 제공합니다. 
- 또한 Kafka Streams 라이브러리를 이용해서 Kafka 입출력에 사용하는 스트림 처리 애플리케이션을 비교적 쉽게 구현할 수 있습니다. 

**전달 보증**
- kafka는 메시지를 잃지 않고 전달하는데 있어 세가지 수준의 보증 기능을 제공합니다. 
![](https://blog.kakaocdn.net/dn/29G5V/btqVII385FT/5KV3bSh3ULUpepmfmC0Wo0/img.png)

