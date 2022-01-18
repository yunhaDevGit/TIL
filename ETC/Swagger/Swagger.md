## Swagger란?

프로젝트를 처음 개발하거나 혹은 유지보수를 진행할 때, API 서버가 어떤 Spec을 가진 데이터를 주고 받는지에 대한 문서작업이 반드시 필요합니다. 

하지만 이러한 문서 작업은 시간이 많이 들고, API가 수정될 때마다 문서도 함께 수정해줘야 하는 번거로움이 있습니다. 

또한, 문서 작업을 하면서 오타 및 내용 누락 등 잘못된 정보가 기입되는 경우도 종종 발생하게 됩니다. 

Swagger(스웨거)는 개발자가 REST API 서비스를 설계, 빌드, 문서화할 수 있도록 하는 오픈 소스 소프트웨어 프레임워크입니다. 


## Swagger 기능

**API Design (API 설계)**

Swagger-editor를 통해 API를 문서화하고 빠르게 명세할 수 있습니다.

**API Development**

Swagger-codepen을 통해 작성된 문서를 통해 SDK를 생성하여 빌드 프로세스를 간소화 할 수 있도록 도와줍니다.

**API Documentation**

Swagger-UI를 통해 작성된 API를 시각화시켜줍니다.

**API Testing**

Swagger-Inspector를 통해 API를 시각화하고 빠른 테스팅을 진행할 수 있습니다.

**Standardize**

Swagger-hub를 통해 개인, 팀원들이 API 정보를 공유하는 Hub입니다.


## Swagger 사용이유

개발하는데에 있어, 테스트는 매우 중요합니다.

API 사용자가 Open API를 사용하기 전에 테스트 할 수 있는 방법에는 어떤게 있을까?

Postman과 같은 API test tool 사용

HTML/JS로 프로토 타입 만든 후 테스트

API 제공자가 Test-bed 제공


API 개발 및 유지보수를 하는 과정에서 Postman을 가장 많이 사용하였습니다.

물론 Postman은 정말 잘 만든 API test-tool로 RESTful 한 API를 테스트하는데 충분합니다.


하지만 REST API는 화면이 없기 때문에 API를 사용자에게 제공하기 위해서는 API 문서는 필수적입니다. 

즉, 사용자는 API문서에 의존하게 됩니다. 

직접 문서를 작성하게 되면 API가 변경될 때마다 문서를 지속적으로 업데이트를 해줘야 합니다.

뿐만 아니라 직접 문서를 작성하는 과정에서 잘못된 정보가 작성될 수 있습니다. 이로 인해 API 사용에 어려움이 발생할 수 있는데, Swagger를 사용하면 개발하는 과정에서 어노테이션 몇 개만 추가해주면 쉽게 문서를 만들 수 있습니다.

또한 Swagger를 사용하면 문서 화면에서 API를 바로 테스트 할 수 있는 Test-bed를 제공할 수 있습니다.



Spring Boot에 Swagger를 적용하여 위와 같이 API 문서에 대한 설명 뿐만 아니라 각 파라미터 항목에 대한 설명도  추가할 수 있습니다.

또한, Response Message 설정 등 사용자가 API 테스트 하기 좋은 환경을 Annotation을 통해 설정하여 제공할 수 있습니다.

다음에는 Spring Boot + Gradle 프로젝트에 Swagger 연동하는 방법과 각 파라미터 및 API 문서에 대한 설정 및 설명을 위한 Annotation에 대해 자세히 알아보는 시간을 갖도록 하겠습니다.

감사합니다.



참고자료
https://swagger.io/docs/specification/about/
https://velog.io/@banjjoknim/Swagger
https://sanghaklee.tistory.com/50
