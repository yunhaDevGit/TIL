## Spring Cloud Gateway

API Gateway를 찾으면 Spring Cloud Zuul을 가장 많이 볼 수 있다. 

Spring Cloud 초기 버전에서는 Netflix OSS에 포함된 컴포넌트 중 하나로 API Gateway를 구현할 수 있는 zuul을 사용했다.

Spring Cloud + Zuul의 형태를 Spring Cloud Zuul이라고 하는데, zuul은 서블릿 프레임워크 기반으로 만들어졌기 때문에 동기, 블로킹 방식으로 서비스를 처리한다.

비동기, 논블로킹 방식이 대세가 되면서 해당 방식을 지원하는 zuul2가 나왔지만 zuul은 Spring 생태계 취지와 맞지 않아 SPring Cloud는 zuul을 사용하지 않고 Spring Cloud Gateway를 새로 만들었다.

Spring Cloud 또한 zuul2와 같이 비동기, 논블로킹 방식을 지원하며, Spring 기반으로 만들어졌기 때문에 Spring과 호환이 좋다. 

*SPring Cloud Gateway는 Netty 런타임 기반으로 동작하므로 서블릿 컨테이너나 WAR로 빌드 된 경우 동작하지 않는다*


### 용어
- Route(경로) : 게이트웨이의 기본 골격. ID, 목적지 URI, 조건부 집합, 필터 집합으로 구성. 조건부가 맞으면 해당 경로로 이동

- Predicate(조건부) : 조건부를 통해 Header나 Parameter같은 HTTP 요청의 모든 항목을 비교할 수 있다

- Filter(필터) : 특정 팩토리로 구성된 Spring Framework Gateway Filter 인스턴스. Filter에서는 다운 스트림 요청 전후에 요청/응답을 수정할 수 있다.




### 동작 원리
아래의 다이어그램은 Spring Cloud Gateway가 전체적으로 동작하는 방식에 대한 전체적인 개요이다.

![Spring Cloud Gateway Diagram](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/images/spring_cloud_gateway_diagram.png)

Client는 Spring Cloud Gateway로 요청을 보내고, 만약 해당 요청이 route와 일치하는 것이 있다 판단할 경우 Gateway Handler Mapping이 Gateway Web Handler에게 해당 요청을 전달합니다. 

이 핸들러는 해당 요청에 대한 filter chain을 이용해 실행한다. 필터가 점선으로 구분되는 이유는 필터가 프록시 요청을 보내기 전후에 로직을 실행할 수 있기 때문이다.

사전 필터가 실행 된 후 프록시 요청을 보내고, 사후 필터가 실행된다.

