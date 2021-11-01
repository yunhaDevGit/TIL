# **Kafka (카프카)**

Kafka는 Pub-Sub 모델의 메시지 큐이고, 분산 환경에 특화되어 설계되어 있다는 특징을 가진다.  
각각의 메시지 큐에 장단점이 있겠지만, Kafka는 대용량 처리에 특화되어 설계되어 있기 때문에 여러 요청이 들어오는 대규모 서비스에 적용 시 다른 메시지 큐에 비해 좋은 성능을 보여준다. 그 외에도 클러스터 구성, fail-over, replication과 같은 여러 가지 특징들을 가진다.  

![](https://blog.kakaocdn.net/dn/b4oNnI/btrilToAOtc/mijTgChNZdsI91DtIkITvK/img.png)

## **물리적 구성요소**

### **Broker**

-   **메시지 수신 및 전달을 처리하는 역할. Kafka Server를 의미**
-   Broker는 하나의 서버 당 하나의 프로세스로 동작
    Broker는 클러스터로 구성할 수도 있는데 브로커를 추가함으로써 스케일 아웃 방식의 처리량 향상이 가능.  
    브로커가 받은 데이터는 모두 디스크에 저장되면 디스크 용량에 따라 일정 기간 데이터 보존이 가능.  
      
    카프카 브로커의 기본 replication은 3개로, 브로커 전체 장애나 replication 할 수 없는 환경에서는 가용성이 보장되지 않는다.  
    브로커 1개 장애 시에는 replication이 3대이므로 서비스가 지속되나, 브로커 2개 장애 시에는 replication이 안된다.  
    replication 수는 3으로 유지하면서 브로커를 유지하거나, 1대 장애도 즉시 복구 하는 전략을 택함.

### **Producer**

-   Producer API를 통해 브로커로 데이터를 보내기 위해 구현된 것.
-   **Producer는 정해진 Topic으로 메시지를 기록**

### **Consumer**

-   Consumer API를 이용하여 **브로커로부터 메시지를 획득**
    -   메시지는 디스크에 저장되어 있기 때문에 디스크에 보관되어 있는 중에는 메시지 획득이 가능

### **Zookeeper**

-   kafka에서 **브로커의 분산 처리를 위한 관리 도구로 쓰이는데, 분산 메시징의 토픽, 파티션 등의 메타 데이터를 관리**하기 위한 목적으로 사용.
-   zookeeper에 저장되는 데이터는 모두 메모리에 저장되어 처리량이 크고 속도가 빠르다. 또 zookeeper는 신뢰도를 높이기 위해 앙상블이라는 호스트 세트를 구성할 수 있는데 3, 5 등 홀수 개로 구성하는 것이 일반적인데 과반수 방식에 의해 살아 있는 노드 수가 과반 수 이상 유지된다면 지속적인 서비스가 가능해진다.
    -   zookeeper 앙상블 3대 구성 후 노드 1대 다운 -> 과반수 유지되므로 서비스 가능
    -   zookeeper 앙상블 3대 구성 후 노드 2대 다운 -> 과반수 유지되지 않아 서비스 불가
    -   zookeeper 앙상블 5대 구성 후 노드 2대 다운 -> 과반수 유지되므로 서비스 가능  
          
        
        |   #### znode  
        | -   **데이터를 저장하기 위한 공간 이름으로 폴더** 정도로 이해하면 쉽다. |
        | -   여러 대의 서버를 클러스터인 앙상블로 묶고, 분산 애플리케이션들이 각각 클라이언트가 되어 zookeeper 서버들과 연결을 맺은 후 상태 정보를 주고 받는다. 상태 정보는 zookeeper의 znode라는 곳에 Key-Value 형태로 저장되고, znode의 데이터를 이용하여 서로 데이터를 주고 받는다. |
        | --- |
        

**논리적 구성요소**

### **Topic**

-   Topic은 **메시지가 생산되고 소비되는 주제** (이벤트가 쓰이는 곳)
    -   예를 들어, 카톡 단체방 A, B가 있는데 A방으로 보낸 메시지가 B방에 노출되면 안되는 것처럼 A방에서 생산된 메시지는 A방에 존재하는 사람들(구독한 사람)에게만 보여져야 하는 것과 같은 논리.

### **Partition**

-   **Topic에 대한 대량 메시지 입출력을 지원하기 위해 브로커의 데이터를 읽고 쓰는 단위**
-   Topic을 구성하는 파티션은 브로커 클러스터 내에 분산 되어 있어 Producer로부터의 데이터를 수신하고, Consumer로 배달을 담당.
    -   파티션을 브로커에 어떻게 배치하는 지에 대한 정보는 Broker 측에 있다.  
        Producer 및 Consumer API는 파티션 정보는 은폐하여 통신하므로 Topic만 지정하면 된다.  
        (물론 특정 파티션을 지정한 송수신도 가능)
    -   카프카 브로커는 파티션 별 leader가 다르고 이 leader를 통해서만 데이터를 처리할 수 있다.  
        leader는 데이터를 받고 나서 나머지 2대의 노드에 복제한다. 복제 시 최초 데이터는 leader가 있는 동일 서버에 저장하고 두 번째부터는 나머지 서버에 랜덤하게 저장된다다.

### **Consumer Group**

-   Producer는 여러 파티션을 통해 메시지를 생산하는데, 이에 대응하는 Consumer 입장  
    여러 소비자가 읽어가면 효율적이기 때문에, 동일 기능을 수행하는 애플리케이션을 여러 개 실행하여 메시지 처리를 할 수 있다.  
    즉 인스턴스 간 메시지 부하 분산이 된다
-   사용 이유?
    -   **고가용성 확보**
        1.  Consumer 그룹 내 여러 인스턴스가 있다면 하나가 장애가 나도 **연속적인 작업이 가능**하게 된다
        2.  Consumer가 메시지를 가져가는 속도보다 메시지가 쌓이는 속도가 빠르다면 읽지 않는 수가 증가하므로 Consumer 개수를 증가시켜 **균형을 유지**할 수 있게 된다. 
        3.  ******offset 관리** -**** Partition에는 하나의 Consumer 인스턴스만 접근할 수 있기 때문에, 특정 Consumer에 에러가 발생했을 시 다른 Consumer 인스턴스는 에러가 발생한 Consumer가 소비하던 Partition을 소비한다.  
              
            즉. Consumer가 다운될 때를 대비해 Consumer Group의 Consumer 인스턴스들은 offset을 공유하고 있고, 이를 통해 고가용성이 확보된다.

-   -   **Partition 접근하는 Consumer 관리**
        -   Consumer Group 내에서 Consumer 인스턴스들은 Topic 내에 Partition에서 다음에 소비할 offset이 어디인지 공유하면서 메시지를 소비한다. 그렇기 때문에 **다음에 소비할 offset을 잘 관리할 수 있다.**
            -   예를 들어 Consumer Group이 없을 경우, 하나의 Partition에 2개의 Consumer가 동시에 접근한다면 어떤 Consumer가 몇 번 offset을 소비해야 하는지 알 수 없다. 즉, Consumer Group을 통해 하나의 partition에는 하나의 Consumer 인스턴스만 접근할 수 있도록 관리한다.

### **Offset**

-   **파티션의 각 메시지를 식별할 수 있는 유니크한 값**
-   파티션 단위로 메시지 위치를 나타내는 Offset이라는 정보를 통해 Consumer가 취득하는 메시지의 범위 및 재시도를 제어
    -   메시지를 소비하는 Consumer가 읽을 차례를 의미하므로 파티션마다 별도로 관리
    -   0부터 시작하여 1씩 증가

[https://sarc.io/index.php/miscellaneous/1890-kafka-core-component](https://sarc.io/index.php/miscellaneous/1890-kafka-core-component)

[https://victorydntmd.tistory.com/344](https://victorydntmd.tistory.com/344)
