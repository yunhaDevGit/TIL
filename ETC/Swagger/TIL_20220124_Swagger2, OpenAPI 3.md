## Swaagger
Swagger는 개발자가 REST 웹 서비스를 설계, 빌드, 문서화, 소비하는 일을 도와주는 대형 도구 생태계의 지원을 받는 오픈 소스 소프트웨어 프레임워크입니다.

Swagger 2.x 의 경우 기업별로 OpenAPI를 제공하는 명세를 사용하였지만 OpenAPI initiative 라는 그룹으로 통합하여 공통된 Spec인 Open API specification을 

제공하기로 하였는데 이때부터 OpenAPI 3.x 로 명명되었습니다.

저희는 통합 규약을 따르기 위해 OpenAPI 3.x 를 사용하기로 하였습니다.

Swagger 2.x에서 OpenAPI 3.x로 변경됨에 따라 Annotation도 변경되었습니다. 변경 Annotation은 아래와 같습니다.
![image](https://user-images.githubusercontent.com/74949294/150766328-7c9f22af-5b40-4a88-a361-b2165d0aebb2.png)

### gradle dependencies
```
implementation 'io.springfox:springfox-boot-starter:3.0.0'
implementation 'io.springfox:springfox-swagger-ui:3.0.0'
```

### swagger-ui url
- http://{service_ip}:{service_port/swagger-ui/index.html
   - ex) http://192.168.120.4:5000/swagger-ui/index.html

### Swagger Config
```java
@Configuration
public class SwaggerConfig {

  private String version = "0.1.0.0";

  @Bean
  public Docket api() {
    return new Docket(DocumentationType.OAS_30)
        .useDefaultResponseMessages(false)
        .select()
        .apis(RequestHandlerSelectors.basePackage("com.innogrid.cloudit6"))
        .paths(PathSelectors.any())
        .build()
        .apiInfo(apiInfo());
  }

  private ApiInfo apiInfo() {
    return new ApiInfoBuilder()
        .title("Cloudit V6.0")
        .description("Cloudit V6.0 API Documentation")
        .version(version)
        .build();
  }
}
```

### Swagger 2.x와 달라진 OpenApi 3.x annotation
- @Api -> @Tag
- @ApiIgnore -> @Parameter(hidden = true) or @Operation(hidden = true) or @Hidden
- @ApiImplicitParam -> @Parameter
- @ApiImplicitParams -> @Parameters
- @ApiModel -> @Schema
- @ApiModelProperty(hidden = true) -> @Schema(accessMode = READ_ONLY)
- @ApiModelProperty -> @Schema
- @ApiOperation(value = "foo", notes = "bar") -> @Operation(summary = "foo", description = "bar")
- @ApiParam -> @Parameter
- @ApiResponse(code = 404, message = "foo") -> @ApiResponse(responseCode = "404", description = "foo")

https://springdoc.org/#migrating-from-springfox
