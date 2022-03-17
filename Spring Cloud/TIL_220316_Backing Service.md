# Backing Service

Backing Service란, 어플리케이션이 실행되는 가운데 네트워크를 통해서 사용할 수 있는 모든 서비스입니다. MySQL과 같은 데이터베이스, 캐쉬 시스템, SMTP 서비스 등 애플리케이션과 통신하는 모든 attached Resource들을 지칭하는 포괄적인 개념입니다. 

### Micro Service의 Backing Service 특징

마이크로 서비스에 Backing Service는 message queue를 활용한 비동기 통신 패턴을 많이 사용합니다. 현대 MSA 특징 중 하나는 하나의 Micro Service에 이벤트(장애 발생, 트래픽 증가, 소스 반영 등)가 발생할 경우 Micro Service 오케스트레이션이 진행되며, Micro Service의 신규 생성, 재생성, 서비스 인스턴스의 삭제 등의 작업이 빈번하게 이루어집니다. 

![image](https://user-images.githubusercontent.com/74949294/158789526-d6dc21ce-d503-43ed-98f3-f8f1bf713668.png)


MSA에서는 하나의 비즈니스 프로세스를 수행하기 위해 여러 Micro Service 간의 통신을 합니다. 

만약 여러 서비스들이 강하게 결합되어 처리될 경우, Orchestration이 진행되면서 진행 중인 트랜잭션이 끊기고 해당 서비스 요청을 보존할 수 없게 됩니다. 

MSA에서는 Message Queue를 활용하여 트랜잭션을 분리하는 전략을 많이 취하는데, 특히 서비스의 영속성 (서비스에 장애가 발생하더라도 event message가 consumer에 전달되는 것을 보장함)을 유지할 수 있다는 장점이 있습니다.
