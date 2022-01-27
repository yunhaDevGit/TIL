# Swagger

- Swagger는 개발자가 REST 웹 서비스를 설계, 빌드, 문서화, 소비하는 일을 도와주는 대형 도구 생태계의 지원을 받는 오픈 소스 소프트웨어 프레임워크입니다.
- Swagger 2.x 의 경우 기업 별로 OpenAPI를 제공하는 명세를 사용하였지만 OpenAPI initiative 라는 그룹으로 통합하여 공통된 Spec인 Open API specification을 제공하기로 하였는데 이때부터 OpenAPI 3.x 로 명명되었습니다.
- 저희는 통합 규약을 따르기 위해 OpenAPI 3.x 를 사용하기로 하였습니다.
- Swagger 2.x에서 OpenAPI 3.x로 변경됨에 따라 Annotation도 변경되었습니다. 변경 Annotation은 아래와 같습니다.
![image](https://user-images.githubusercontent.com/74949294/151315075-966f95cf-49f2-4697-a104-393662e44e7e.png)



## gradle dependencies

![image](https://user-images.githubusercontent.com/74949294/151315029-a251b464-8741-4b94-aa3d-b51ac7306ff3.png)

![image](https://user-images.githubusercontent.com/74949294/151315103-be6e029c-9080-4b3e-88fb-3fec96b247dd.png)

현재 Maven Repository를 확인해보면 SpringFox에서도 2020년 7월 업데이트 되어 OpenAPI 3.x 의존성을 제공하지만 SpringFox와 달리 Springdoc에서 `OpenAPI UI`라는 이름으로 꾸준히 업데이트 되는 것을 확인 할 수 있습니다.

때문에 더 최신이며 안정적인 `Springdoc OpenAPI UI`를 통한 의존성 주입을 하겠습니다.

```
// <https://mvnrepository.com/artifact/org.springdoc/springdoc-openapi-ui>
implementation group: 'org.springdoc', name: 'springdoc-openapi-ui', version: '1.6.4'
```

> springdoc-openapi v1.6.4의 지원 기능
> 
> - OpenAPI 3
> - Spring Boot (v1, v2)
> - JSR-393 (@NotNull, @Min, @Max, @Size 등)
> - Swagger-ui
> - OAuth2
> - GraalVm 네이티브 이미지

## swagger-ui url

이전 버전인 Swagger 2.x와 달리 OpenAPI 3.x의 Swagger UI 경로가 달라졌습니다.

- http://{service\_ip}:{service\_port/swagger-ui/index.html
    - ex) [http://192.168.120.6:9696/swagger-ui/index.html](http://192.168.120.4:5000/swagger-ui/index.html)

## Swagger Config

- Swagger Config 공통 처리 필요

```java
import io.swagger.v3.oas.models.ExternalDocumentation;
import io.swagger.v3.oas.models.OpenAPI;
import io.swagger.v3.oas.models.info.Info;
import io.swagger.v3.oas.models.info.License;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class SwaggerConfig {

  private String version = "0.1.0.0";

  @Bean
  public OpenAPI openAPI() {
    return new OpenAPI()
        .info(new Info().title("CSQ")
            .description("CSQ API Documentation")
            .version(version)
            .license(new License().name("Apache 2.0").url("<http://csq.licence>)"))
        .externalDocs(new ExternalDocumentation()
            .description("CSQ Wiki Documentation")
            .url("<https://github.com/EzStudy/CSQ/blob/master/README.md>"));
  }

//  SpringFox Library
//  @Bean
//  public Docket api() {
//    return new Docket(DocumentationType.SWAGGER_2)
//        .useDefaultResponseMessages(false)
//        .select()
//        .apis(RequestHandlerSelectors.basePackage("com.innogrid.cloudit6")
//        .paths(PathSelectors.any())
//        .build()
//        .apiInfo(apiInfo());
//  }

//  private ApiInfo apiInfo() {
//    return new ApiInfoBuilder()
//        .title("Cloudit V6.0")
//        .description("Cloudit V6.0 API Documentation")
//        .version(version)
//        .build();
//  }
}

```

## Swagger Annotation 종류

**공통 규칙**

- description의 경우, 한글로 작성하되 보편적으로 영어로 읽는 단어는 발음대로 작성한다.
    - 예시)
        - ID -> 아이디
        - password -> 패스워드
        - name -> 이름 (네임 x)
        - description -> 설명 (디스크립션 x)

## RestController에서 사용되는 Annotation

**1. @Tag(name ="")**

- Controller 이름을 작성합니다.
- "xxxController" class 의 경우 name는 "xxx Controller" 으로 작성합니다.
    - ex) UserController -> User Controller

**2. @Operation(summary = "", description = "")**

- 메서드 설명과, 상세 설명을 작성할 수 있다. 설명에는 html 태그 사용도 가능합니다.
- summary, description 로 구분이 되며 summary 는 단답형을 사용하고, description 는 완성형 문장을 사용합니다.
- 리턴 타입이 단수인 경우 "단일", 복수인 경우 "목록" 키워드를 사용합니다.
    - @Operation(summary = "단일 회원 조회", description = "사용자 아이디를 통해 회원을 조회합니다.")
    - @Operation(summary = "회원 목록 조회", description = "조건에 해당하는 회원 목록을 조회합니다.")
- 예시)
    - User 조회
        - @Operation(summary = "단일 사용자 조회", description = "사용자 아이디를 통해 사용자를 조회합니다.")
        - @Operation(summary = "사용자 목록 조회", description = "조건에 해당하는 사용자 목록을 조회합니다.")
    - User 생성
        - @Operation(summary = "사용자 생성")
    - User 업데이트
        - @Operation(summary = "사용자 정보 업데이트")
    - User 삭제
        - @Operation(summary = "사용자 삭제", notes = "사용자 아이디를 통해 사용자를 삭제합니다.)

**3. @Parameter(name = "", description = "", required = true)**

- 파라미터의 설명을 적용해준다. required = true 속성 설정도 가능하다.
- @Valid @RequestBody, RequestBody 에는 해당 애노테이션을 사용하지 않습니다.
- @PathVariable @Parameter(value = "")
- @RequestParam @Parameter(value = "", required="")

**4.~~@ApiResponse(responseCode = 200, description = "")~~**

- 각 메서드에 해당하는 responseMessage를 적용할 수 있다.
- **해당 애노테이션은 사용하지 않습니다.**
- RestController Response 의 경우 공통 AOP 를 구현하여 처리하였습니다.

## Dto 에서 사용되는 Annotation

**1.** DTO 클래스의 경우
**@Schema(description = "") 어노테이션을 사용하여 설명을 작성합니다**

- description = 어떠한 케이스에 사용 되는지 단답형 문장으로 표현
- 예시)
    - User 생성
        - @Schema(description = "사용자 생성을 위한 객체")
    - User 조회
        - @Schema(description = "사용자 조회를 위한 객체")
    - User 갱신
        - @Schema(description = "사용자 갱신을 위한 객체")
    - User 반환
        - @Schema(description = "사용자 정보 반환을 위한 객체)

- ***DTO 클래스의 하위 클래스가 존재할 경우, DTO 클래스로 분류합니다.***
    - CreateLoabalancerRequestDto에서 로드밸런서 멤버 정보를 저장하는 클래스가 사용되는 경우, 해당 클래스도 DTO로써, LoabalancerMemberDto라고 명명합니다
    - 또한 하위 클래스인 LoabalancerMemberDto의 @Schema(description = "")은 다음과 같이 작성합니다.
    - 예시)
        - @Schema(description = "로드밸런서 멤버 정보가 담긴 객체")

**2. @Schema(description = "", required = true, description="", defaultValue="", allowableValues={"0", "1", "2"}, example = "")**

- 공통 규칙 준수
- description = 해당 필드를 명사로 표현
- @Schema annotation의 Optional Element를 사용하여 Validation 처리
    - allowableValues
    - maximum
    - minimum
    - defaultValue
    - description
    - nullable

참고 : [https://docs.swagger.io/swagger-core/v2.0.0-RC3/apidocs/io/swagger/v3/oas/annotations/media/Schema.html#nullable--](https://docs.swagger.io/swagger-core/v2.0.0-RC3/apidocs/io/swagger/v3/oas/annotations/media/Schema.html#nullable--)

# Reference

[1] What's the Difference Between OpenAPI 2.0, 3.0, and 3.1?, [https://blog.stoplight.io/difference-between-open-v2-v3-v31](https://blog.stoplight.io/difference-between-open-v2-v3-v31)
[2] OpenAPI Specitfication, [https://github.com/OAI/OpenAPI-Specification/blob/main/versions/3.0.2.md](https://github.com/OAI/OpenAPI-Specification/blob/main/versions/3.0.2.md)
[3] [Spring] Swagger를 이용한 REST API 문서화를 해보자\!\, [https://data-make.tistory.com/550](https://data-make.tistory.com/550)
[4] Jakarta Bean Validation specification, [https://beanvalidation.org/2.0/spec/#builtinconstraints](https://beanvalidation.org/2.0/spec/#builtinconstraints)
[5] Migration from SpringFox, [https://springdoc.org/#migrating-from-springfox](https://springdoc.org/#migrating-from-springfox)
