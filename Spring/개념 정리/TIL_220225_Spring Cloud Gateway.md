# Spring Cloud Gateway(SCG)

### Spring Cloud Gateway란?

MSA 환경에서 사용하는 API Gateway 중 하나로 Spring5, Spring Boot2, Project Reactor로 구축된 API Gateway입니다. 

Spring Cloud Gateway는 API 라우팅 및 보안, 모니터링/메트릭 등의 기능을 간단하고 효과적인 방법으로 제공합니다. 

### API Gateway 사용 이유

- 유입되는 모든 요청/응답이 통하기 때문에 인증/보안을 적용하기 좋습니다.
- URI에 따라 서비스 엔드포인트를 다르게 가져가는 동적 라우팅이 가능해집니다.
예를 들면, 도메인 변경 없이 레거시 시스템을 신규 시스템으로 점진적으로 교체해 나가는 작업을 쉽게 할 수 있습니다.
- 모든 트래픽이 통하기 때문에 모니터링 시스템 구성이 단순해집니다.
- 동적 라우팅이 가능하므로 신규 스팩을 서비스 일부에만 적용하거나 트래픽을 점진적으로 늘려나가는 테스트를 수행하기에 수월해집니다.

### Spring Cloud Zuul과의 차이점

API Gateway에 대해 찾다 보면 Spring Cloud Zuul에 대한 내용이 가장 많이 나옵니다. 

이 둘의 차이를 알아보겠습니다.

Spring Cloud의 초창기 버전에서는 Netflix OSS(Open Source Software)에 포함된 컴포넌트 중 하나로서 API Gateway 패턴을 구현할 수 있는 Zuul을 사용했습니다. Spring Cloud + Zuul의 형태를 Spring Cloud Zuul이라고 합니다. Zuul은 서블릿 프레임워크 기반으로 만들어졌기 때문에 동기, 블로킹 방식으로 서비스를 처리합니다. 

그러다 비동기, 논블로킹 방식이 대세가 됨에 따라 해당 방식을 지원하는 Zuul2가 나오게 됩니다. 하지만 Zuul은 Spring 생태계의 취지와 맞지 않아 Spring Cloud Gateway에서는 Zuul2를 사용하지 않고 API Gateway 패턴을 구현할 수 있는 Spring Cloud Gateway를 새로 만듭니다. 

Spring Cloud Gatway도 Zuul2와 마찬가지로 **비동기, 논블로킹 방식**을 지원합니다. 

또한, Spring  기반으로 만들어졌기 때문에 Spring 서비스와의 호환도 좋습니다. 

Spring Cloud Gateway는 **Netty**런타임 기반으로 동작합니다. 때문에 서블릿 컨테이너나 WAR로 빌드된 경우 동작하지 않습니다.
