# Springfox Swagger vs SpringDoc

Spring 기반의 API Docs를 가능하게 해주는 건 크게 Springfox Swagger와 SpringDoc이 있습니다. 

그러나 최근 Spring에서 밀고 있는 건 **SpringDoc**입니다. Springfox Swagger를 잘 발전시킨 것이 **SpringDoc**이기 때문입니다. 

Springfox Swaggerrk 2018년까지 많이 사용되다 2018년 6월을 마지막으로 업데이트가 중지되었고 그 사이 2019년 7월에 springdoc이 나와 SpringDoc을 더 많이 사용하고 있습니다. 

그러던 중 2020년 6월 Springfox가 새로운 버전을 만들어 현재는 두 개를 혼용해서 많이 사용하고 있지만, Spring에서 더 선호하는 것도 **SpringDoc**이고, 더 많은 기능을 제공하며, 레거시 코드가 Springfox에 비해 비교적 더 적기 때문에 `SpringDoc`을 사용할 것을 권장합니다.

**SpringFox Maven Repository**

![image](https://user-images.githubusercontent.com/74949294/180396759-b4e02ddd-74be-4e76-b8c2-eb9f9fd41331.png)

**Springdoc Maven Repository**

![image](https://user-images.githubusercontent.com/74949294/180396781-a3f10af6-d0fc-4478-b7d7-3b1a291b765e.png)

### SpringDoc OpenAPI

> **SpringDoc OpenAPI**
- SpringDoc OpenAPI는 OAS 3.0 지원
- springfox에서 지원하지 않는 주석을 지원 (ex, @MatrixParam)
- Spring Boot 2.2.x는 SpringDoc에서만 동작
> 

**springdoc-openapi의 overview**

![image](https://user-images.githubusercontent.com/74949294/180396811-7330b7b7-64f0-4dac-916f-3e2f801efc2a.png)

**dependency**

```xml
<dependency>
	<groupId>org.springdoc</groupId>
	<artifactId>springdoc-openapi-ui</artifactId>
	<version>1.5.9</version>
</dependency>
```

위의 dependency를 **pom.xml**에 추가해주면 openapi를 사용할 수 있게 됩니다. 

SpringDoc의 dependencies를 보면 swagger-ui가 함께 의존성 추가된 것을 확인할 수 있습니다. 

![image](https://user-images.githubusercontent.com/74949294/180396846-8a4b834c-624f-4569-962d-870404f6ff15.png)

즉, **핵심 로직(swagger-ui)와** **Spring에서 핵심 로직을 활용해서 더 잘 쓸 수 있게 해주는 라이브러리(springdoc-openapi)**라고 생각하면 됩니다.

[https://velog.io/@soyeon207/우당탕탕-Swagger-적용기](https://velog.io/@soyeon207/%EC%9A%B0%EB%8B%B9%ED%83%95%ED%83%95-Swagger-%EC%A0%81%EC%9A%A9%EA%B8%B0)
