# Domain Driven Design (DDD)

### Domain이란?

사전적 의미는 "정보와 활동의 영역"

흔히 '어플리케이션 내의 로직들이 관여하는 정보와 활동의 영역'이다

예를 들어 온라인 서접 시스템을 구현한다고 할 때, 소프트웨어로 해결하고자 하는 문제의 영역인 **온라인 서점**이 도메인이 된다.

한 도메인은 다시 여러 개의 하위 도메인으로 나뉠 수 있다. (온라인 서점의 하위 도메인 : **상품, 회원, 주문, 정산 등**)

즉, 도메인은 **실세계에서 사건이 발생하는 집합**이라고 생각하면 된다.



**"애플리케이션 내의 로직들이 관여하는 정보와 활동의 영역"**



### DDD란?

도메인 주도 디자인이란 개발을 함에 있어 **도메인이 중심이 되는 개발 방식**을 말한다.  

이는 소프트웨어의 연관된 부분들을 연결하여 계속 해서 진화하는 새로운 모델을 만들어 나가 복잡한 어플리케이션을 만드는 것을 쉽게 해준다.

DDD의 핵심 목표는 Loose Coupling, High Cohesion이다.



#### DDD의 세가지 주요 원리

1. 핵심 도메인과 그 기능에 집중하라
2. 도메인의 모델을 정교하게 구축하라
3. 어플리케이션 모델을 발전시키고 새롭게 생기는 도메인 관련 이슈를 해결하기 위해 도메인 전문가와 끊임없이 협력해라



### 설계 방식

**크게 3가지 Layer로 구분한다**

1. **Application Layer** : 주로 도메인과 Repository를 바탕으로 실제 서비스(API)를 제공하는 계층. 소프트웨어가 수행할 작업을 정의하고 표현적 도메인 개체가 문제를 해결할 수 있도록 지시
2. **Domain Layer** : Entity, VO를 활용하여 도메인 로직이 진행되는 계층. 비즈니스 개념, 비즈니스 상황 정보, 비즈니스 규칙을 나타내는 작업을 담당한다.
3. **Infrastructure Layer** : 쉽게 말하면 외부와의 통신 (ORM, DB, NoSql)을 담당하는 계층. 최초에 도메인 엔터티에 있던 데이터가 데이터베이스나 기타 영구 저장소에 유지되는 방식



https://ppiyo5.tistory.com/21

https://docs.microsoft.com/ko-kr/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/ddd-oriented-microservice

https://huisam.tistory.com/entry/DDD

