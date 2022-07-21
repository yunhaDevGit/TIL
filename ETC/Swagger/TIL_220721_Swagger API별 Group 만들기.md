# Swagger API별 Group 만들기

springdoc-openapi를 사용할 경우 OpenAPI bean을 사용하여 Swagger-ui를 구성하는 메세지를 설정할 수 있습니다. 

```java
@Configuration
public class SwaggerConfig {

  private String version = "0.1.0.0";

  @Bean
  public OpenAPI openAPI() {
    return new OpenAPI()
        .info(new Info().title("Test Swagger")
            .description("Test API Documentation")
            .version(version)
            .license(new License().name("Apache 2.0").url("http://license.com")))
        .externalDocs(new ExternalDocumentation()
            .description("Test Wiki Documentation")
            .url("http://test.test.com"));
  }
}
```

OpenAPI bean에 설정한 내용들이 Swagger-ui 상단에 나오는 걸 확인할 수 있습니다. 

![image](https://user-images.githubusercontent.com/74949294/180153171-8b7d4a49-6898-4ea8-845c-cd20e8852a7b.png)

위와 같이 설정을 하면 모든 API가 한 화면에 담겨 보이게 됩니다. 모든 API를 한 화면에 보이게 하고 싶을 경우 위와 같이 `OpenApi`를 사용해서 설정하면 됩니다. 

하지만 여기서는 API URI가 `pathsToMatch`에 해당하는 API들끼리 그룹핑 해서 보여줄 수 있습니다. 

즉, `GroupedOpenAPI` 설정을 하면 그룹 설정된 api들만 문서에 노출 시킬 수 있습니다.
```Java
@Configuration
public class SwaggerConfig {

  private String version = "0.1.0.0";
  @Bean
  public GroupedOpenApi test1Api() {
    return GroupedOpenApi.builder()
        .group("test1-api")
        .pathsToMatch("/api/test1/**")
        .build();
  }

  @Bean
  public GroupedOpenApi test2Api() {
    return GroupedOpenApi.builder()
        .group("test2-api")
        .pathsToMatch("/api/test2/**")
        .build();
  }

  @Bean
  public GroupedOpenApi test3Api() {
    return GroupedOpenApi.builder()
        .group("test3-api")
        .pathsToMatch("/api/test3/**")
        .build();
  }

  @Bean
  public OpenAPI openAPI() {
    return new OpenAPI()
        .info(new Info().title("Test Swagger")
            .description("Test API Documentation")
            .version(version)
            .license(new License().name("Apache 2.0").url("http://license.com")))
        .externalDocs(new ExternalDocumentation()
            .description("Test Wiki Documentation")
            .url("http://test.test.com"));
  }
}
```

![image](https://user-images.githubusercontent.com/74949294/180153187-55fdf667-48a6-4823-b26a-260f4a2b929e.png)

![image](https://user-images.githubusercontent.com/74949294/180153208-e8d90eff-409f-40a7-9c45-ddaddf2ae682.png)
