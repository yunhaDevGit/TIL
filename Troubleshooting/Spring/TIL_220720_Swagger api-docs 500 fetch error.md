# [Swagger] /v3/api-docs 500 error

회사에서 API 문서를 좀 더 편하고 보기 좋게 만들기 위해 기존에 있던 프로젝트에 Swagger(또는 OpenAPI)를 추가하게 되었습니다.

지난번에 Swagger를 적용했던 경험이 있어 금방 Swagger 설정이 끝날 거라 생각했습니다.

기존에 Swagger를 적용했었던 Project들과는 구조와 설정, 사용 라이브러리 등이 많이 달랐기 때문이었던 건지 바로 설정이 되지 않았습니다.

사소한 것들로 며칠 간 삽질한 경험을 공유하겠습니다. 부디 저 같은 삽질은 안하시길,,,ㅎ

⚠️ 코드는 테스트 코드로 작성했습니다 ⚠️

**project 구조**

```
com
	ㄴ openapi
				ㄴ openapi3test
						 ㄴ config
									ㄴ SwaggerConfig.java
									ㄴ WebSecurityConfig.java
									ㄴ WebMvcConfig.java
						 ㄴ controller
									ㄴ test1
											 ㄴ TestController1.java
									ㄴ test2
											 ㄴ TestController2.java
									ㄴ test3
											 ㄴ TestController3.java
```

- **pom.xml**
    
    아래의 dependency를 추가 후 
    
    ```xml
    <dependency>
    	<groupId>org.springdoc</groupId>
    	<artifactId>springdoc-openapi-ui</artifactId>
    	<version>1.6.8</version>
    </dependency>
    ```
    
- **application.yml**
    
    ```yaml
    server:
    	port: 8090
    ```
    
- **SwaggerConfig.java**
    
    ```java
    @EnableWebMvc
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
    
- **WebSecurityConfig.java**
    
    ```java
    @Configuration
    @EnableWebSecurity
    @EnableGlobalMethodSecurity(securedEnabled = true)
    public class SecurityConfig extends WebSecurityConfigurerAdapter {
    
      ... 
      @Override
      protected void configure(HttpSecurity http) throws Exception {
        http
            .cors().and().csrf().disable()
            .authorizeRequests()
            .antMatchers("/v3/api-docs",
                "/v3/api-docs/**",
                "/swagger-ui.html",
                "/swagger-ui/**").permitAll()
            .anyRequest().authenticated();
      }
    
      ...
    }
    ```
    
- **WebMvcConfig.java**
    
    ```java
    @Configuration
    @EnableWebMvc
    public class WebMvcConfig implements WebMvcConfigurer {
      private static final String API_PATH_PREFIX = "/api";
    
    	...
      @Override
      public void configurePathMatch(PathMatchConfigurer configurer) {
        // TODO: RestController 형태이면 base path 를 설정한다.
        configurer
            .setUseTrailingSlashMatch(false)
            .setUseRegisteredSuffixPatternMatch(true)
            .addPathPrefix(API_PATH_PREFIX,
                HandlerTypePredicate.forAnnotation(RestController.class)
            );
      }
    	...
    }
    ```
    

1. **Failed to load remote configuration** 
    
    위와 같이 설정을 하고 실행해보니 `Failed to load remote configuration`에러가 나왔습니다. 
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c4371dba-eeed-4ca2-a1df-e5dc897b2a7c/Untitled.png)
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ac2f26e9-1d21-456f-8e5e-3e390ab7a06e/Untitled.png)
    
    403 권한 에러가 났습니다.
    
    개발자 도구를 통해 확인해본 결과 default 경로인 `/v3/api-docs/swagger-config`가 아닌 **`/api**/v3/api-docs/swagger-config`로 요청하는 것을 확인할 수 있었습니다. 
    
    추가로 application.yml에 swagger 설정을 하지 않았는데 요청 앞에 `/api`가 붙었습니다.
    
    확인해보니 WebMvcConfig.java에 모든 REST API 요청 앞에 `/api`를 붙이는 로직이 있었습니다. 
    
    모든 REST API 공통 사항이기 때문에 WebSecurityConfig.java의 허용 pattern을 변경해주었습니다.
    
    ```java
    @Override
      protected void configure(HttpSecurity http) throws Exception {
        http
            .cors().and().csrf().disable()
            .authorizeRequests()
            .antMatchers("/api/v3/api-docs",
                "/api/v3/api-docs/**",
                "/swagger-ui.html",
                "/swagger-ui/**").permitAll()
            .anyRequest().authenticated();
      }
    ```
    
2. **Swagger Petstore**
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a3d05134-3dc7-450f-b1d0-5c4659ec2215/Untitled.png)
    
    `WebSecurityConfig.java`에 올바른 경로를 입력해준 결과 swagger-config는 정상적으로 잘 불러왔습니다.
    
    그러나 계속해서 프로젝트의 API가 아닌 샘플 페이지가 나왔습니다. 
    
    샘플 페이지가 나오지 않게 하기 위해 `application.yml`의 설정을 변경해주었습니다. 
    
    ```yaml
    springdoc:
      swagger-ui:
        disable-swagger-default-url: true
    ```
    
3. **Failed to load API definition**
    
    위의 설정을 통해 샘플 페이지가 나오진 않았지만 /api-docs를 호출하는 과정에서 계속해서 API를 로드 하지 못하는 이슈가 발생했습니다.
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a97ce721-76d6-49a5-91c2-782f2fef0bbb/Untitled.png)
    
    ⬆️ 해당 이슈 재연이 불가하여 비슷한 에러 화면으로 대체하였습니다. 
    
    ```json
    {
    	error: "INTERNAL_SERVER_ERROR"
    	message: "요청을 수행하는 도중 오류가 발생하였습니다"
    	status: 500
    	timestamp: "2022-07-13 20:27:34.491"
    }
    ```
    
    계속해서 Fetch error, 500 error가 발생했습니다.
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8ae47e95-43cb-4029-a0e3-59705f746966/Untitled.png)
    
    위와 같은 로그와 `org.apache.logging.log4j`를 찾지 못한다는 메세지가 나왔습니다. 
    
    설정 문제라고 판단하여 `application.yml`에 다양한 설정을 통해 API를 읽어올 수 있도록 했습니다.
    
    application.yml에 설정 가능한 옵션은 아래와 같습니다. 
    
    ([여기](https://springdoc.org/#swagger-ui-properties)에 더 많은 옵션이 있습니다. 아래 작성된 건 예시입니다 ☺️)
    
    ```yaml
    springdoc:
      version: '@project.version@'
      api-docs:
        path: /api-docs
      default-consumes-media-type: application/json
      default-produces-media-type: application/json
      swagger-ui:
        path: /swagger-ui.html
        disable-swagger-default-url: true
        display-query-params-without-oauth2: true
        doc-expansion: none
      paths-to-match:
        - /api/**
    ```
    
    위의 설정들을 해주었지만 여전히 API를 찾지 못했습니다. 
    
    Controller를 찾지 못하는 건가 싶어 swagger-ui properties 중 스캔 할 패키지를 지정하는 옵션을 추가했습니다. 
    
    ```
    // 패키지 구조
    com
    	ㄴ openapi
    				ㄴ openapi3test
    						 ㄴ config
    									ㄴ SwaggerConfig.java
    									ㄴ WebSecurityConfig.java
    									ㄴ WebMvcConfig.java
    						 ㄴ controller
    									ㄴ test1
    											 ㄴ TestController1.java
    									ㄴ test2
    											 ㄴ TestController2.java
    									ㄴ test3
    											 ㄴ TestController3.java
    ```
    
    아래와 같이 controller가 담긴 패키지들의 부모 패키지를 지정해주었습니다.
    
    ```yaml
    springdoc:
    	packagesToScan: com.openapi.openapi3test.controller
    ```
    
    그러나 여전히 동일한 에러가 발생했습니다. 
    
    반면 각각 모든 패키지들을 `packageToScan`에 지정해주었더니 정상적으로 동작했습니다.
    
    ```yaml
    springdoc:
    	packagesToScan: com.openapi.openapi3test.controller.test1, com.openapi.openapi3test.controller.test2, com.openapi.openapi3test.controller.test3
    ```
    

🎉🎉🎉🎉🎉

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/97534d9d-b28b-4d62-8d52-e503b163b486/Untitled.png)

‼️결론‼️

1. Spring Security를 사용할 경우, `configure(HttpSecurity http)`에 올바른 조건(?)을 추가해줍니다.
2. 스캔 해야 될 패키지가 여러 개이고 제대로 불러오지 못할 경우, `application.yml` 또는 `application.properties`에 `springdoc.packageToScan`에 정확한 패키지 경로를 작성해줍니다.

➕  위의 상황(Failed to API load definition)을 재현하기 위해 application.yml에 작성한 패키지 경로를 지우고, 캐시도 날려보았지만 정상적으로 동작했습니다. 처음에 한 번만 제대로 불러오면 되는 건진,,, 잘 모르겠습니다 😂
