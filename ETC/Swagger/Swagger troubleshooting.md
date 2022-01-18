## Swagger 적용하면서 잘 안된 부분

처음에 가장 최신 버전인 swagger 3.x를 사용하여 Spring Boot Project와 연동하였다.

### build.gradle
```
implementation group: 'io.springfox', name: 'springfox-swagger2', version: '3.0.0'
implementation group: 'io.springfox', name: 'springfox-swagger-ui', version: '3.0.0'
```

### SwaggerConfig.java
```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {

 @Bean
 public Docket api() {
   return new Docket(DocumentationType.SWAGGER_2)
       .useDefaultResponseMessages(false)
       .apiInfo(getApiInfo())
       .select()
       .apis(RequestHandlerSelectors.any()) // api 스펙이 작성되어 있는 패키지를 지정한다.
       .paths(PathSelectors.ant("/*/**"))  // 지정된 URL에 해당하는 요청만 SWAGGER로 만든다
       .build();
 }

 private ApiInfo getApiInfo() {
   return new ApiInfoBuilder()
       .title("Swagger 사용하기")
       .description("Swagger를 활용한 API 문서 자동화")
       .contact(new Contact("Test Swagger", "https://test.test.com/", "test@gmail.com"))
       .version("1.0")
       .build();
 }
}
```

위와 같이 build.gradle에 dependency를 추가해주었고, SwaggerConfig에 설정 정보도 제대로 작성해주었으나 계속해서 swagger-ui가 열리지 않았다.

2.x 버전과 다르게 swagger-ui 접속 url이 http://localshot:8080/swagger-ui/index.html으로 바뀌었기 때문에, 해당 url로 접속해도 계속해서 404 에러가 나왔다.

혹시나 해서 2.x 버전으로 swagger2, swagger-ui 버전을 내리니 http://localhost:8080/swagger-ui.html로 접속했을 때 정상적으로 페이지가 보였다.

아무래도 2.x 버전과 3.x 설정이 달라진 것 같다…

springfox-boot-starter는 필요한 모든 의존성을 다 들고 있어 혹시 의존성 문젠가 싶어 추가해주었더니 swagger-ui가 정상적으로 보였다^^


###build.gradle
```
implementation “io.springfox:springfox-boot-starter:3.0.0”
```

왜 2.x는 springfox-swagger2, springfox-swagger-ui만 추가해줘도 되더니 3.x는…
하여튼 제대로 동작하니까....
