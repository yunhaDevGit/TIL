## Spring Cloud Gateway

API Gateway를 찾으면 Spring Cloud Zuul을 가장 많이 볼 수 있다. 

Spring Cloud 초기 버전에서는 Netflix OSS에 포함된 컴포넌트 중 하나로 API Gateway를 구현할 수 있는 zuul을 사용했다.

Spring Cloud + Zuul의 형태를 Spring Cloud Zuul이라고 하는데, zuul은 서블릿 프레임워크 기반으로 만들어졌기 때문에 동기, 블로킹 방식으로 서비스를 처리한다.

비동기, 논블로킹 방식이 대세가 되면서 해당 방식을 지원하는 zuul2가 나왔지만 zuul은 Spring 생태계 취지와 맞지 않아 SPring Cloud는 zuul을 사용하지 않고 Spring Cloud Gateway를 새로 만들었다.

Spring Cloud 또한 zuul2와 같이 비동기, 논블로킹 방식을 지원하며, Spring 기반으로 만들어졌기 때문에 Spring과 호환이 좋다. 

*SPring Cloud Gateway는 Netty 런타임 기반으로 동작하므로 서블릿 컨테이너나 WAR로 빌드 된 경우 동작하지 않는다*



## Spring Cloud Gateway 구성 요소

### Route

- API Gateway의 가장 기본 요소
- 서비스의 고유한 값인 id, 요청할 uri, Predicate, Filter로 구성
- 요청된 uri의 조건이 predicate와 일치하는지 확인 후, 일치하는 경우 해당 uri 경로로 요청 매핑

### Predicate

- API Gateway로 들어온 요청이 주어진 조건을 만족하는지 확인하는 구성요소
- 하나 이상의 조건을 정의할 수 있으며, 만약 Predicate 조건에 맞지 않을 경우 HTTP 404 Not Found 응답 반환

### Filter

- API  Gateway로 들어오는 요청에 대해 Filter를 적용하여 선처리 및 후처리를 할 수 있게 해주는 구성 요소

## Spring Cloud Gateway 동작원리

![image](https://github.com/yunhaDevGit/TIL/assets/74949294/65f4abe5-0be9-4bd7-8969-cea10b908ef6)

클라이언트에서 들어온 요청은 **Gateway Handler Mapping**을 통해 요청 경로와 일치 여부를 판단하고, **Gateway Web Handler**에서 요청과 관련된 필터 체인을 통해 요청이 전송되게 됩니다. 

이후 적용되는 **Filter**를 통해 요청 또는 응답에 필요한 전처리, 후처리를 할 수 있으며 **Proxy Filter**는 프록시 요청이 처리될 때 수행됩니다.



아래의 다이어그램은 Spring Cloud Gateway가 전체적으로 동작하는 방식에 대한 전체적인 개요입니다.

![Spring Cloud Gateway Diagram](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/images/spring_cloud_gateway_diagram.png)

Client는 Spring Cloud Gateway로 요청을 보내고, 만약 해당 요청이 route와 일치하는 것이 있다 판단할 경우 Gateway Handler Mapping이 Gateway Web Handler에게 해당 요청을 전달합니다. 

이 핸들러는 해당 요청에 대한 filter chain을 이용해 실행한다. 필터가 점선으로 구분되는 이유는 필터가 프록시 요청을 보내기 전후에 로직을 실행할 수 있기 때문이다.

사전 필터가 실행 된 후 프록시 요청을 보내고, 사후 필터가 실행된다.


## 작성 방법

application.yml(또는 .properties)에 작성하는 방법과 Java Code로 작성하는 방법 두 가지가 있습니다.

```yaml
server:
  port: 8000

spring:
  cloud:
    gateway:
      routes:
        - id: first-service
          uri: http://localhost:8081/
          predicates:
            - Path=/first-service/**
        - id: second-service
          uri: http://localhost:8082/
          predicates:
            - Path=/second-service/**
          filters:
            - AddRequestHeader=second-request, second-request-header
            - AddResponseHeader=second-response, second-response-header
```

1. locallost:8000/first-service/~~ 요청이 게이트웨이로 들어옵니다.
2. 요청 받은 uri의 조건을 predicates에서 탐색합니다.
    
    (cloud.gateway.routes.id.predicate의 /first-service/** 가 확인됩니다.)
    
3. 해당 route에 해당되는 uri인 http://localhost:8081/으로 요청을 전달합니다.
