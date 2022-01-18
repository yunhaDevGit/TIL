
Swagger 기본 설정 및 적용

프로젝트는 Spring Boot & Gradle 기반으로 진행하겠습니다.

기본적으로 Spring Boot가 셋팅 되어 있다는 가정 하에 Swagger 관련 설정을 하겠습니다.

1.  build.gradle에 아래의 의존성을 추가해줍니다.
    ```
       implementation “io.springfox:springfox-boot-starter:3.0.0”
    ```
    이 포스팅에서는 Swagger 3.0.0 버전을 사용합니다.
    ‘springfox-boot-starter’는 Swagger를 사용하기 위한 필수 라이브러리 및 API 스펙 명세화 기능 사용 등 여러가지 기능을 UI로 사용하기 위해서 필요한 라이브러리입니다.

2.  프로젝트에 Swagger 설정을 하기 위한 설정 클래스 SwaggerConfig를 만들어줍니다.
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
	.paths(PathSelectors.ant("/*/**")) // 지정된 URL에 해당하는 요청만 SWAGGER로 만든다
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

@EnableSwagger2 : Swagger2 버전을 활성화해줍니다.

Docket : Springfox 프레임워크의 기본 인터페이스가 될 빌더로 구성을 위한 여러 가지 기본값과 편리한 방법을 제공합니다.

(Springfox : spring과 연동하는 자동 문서화 라이브러리)


설정 정보
-   select() : Swagger 설정 이후에 ApiSelectorBuilder 인스턴스를 리턴합니다.
-   apis()
	- api 스펙이 작성되어 있는 패키지를 지정합니다.
	- 컨트롤러가 존재하는 패키지를 basepackage로 지정하여, RequestMapping이 선언된 API를 문서화해줍니다.
(위 예시에서는 RequestMapping이 선언된 모든 API를 문서화 하고 싶기 때문에 RequestHandlerSelector.any()로 지정해주었습니다)
-   paths() : 특정 path만 필터링하여 문서를 만들어줍니다.
-   useDefaultResponseMessages() : swagger에서 제공해주는 응답코드 (200,401,403,404)에 대한 기본 메시지 사용 여부에 대한 설정입니다.
-   apiInfo() : 제목, 설명 등 문서 정보를 작성할 수 있습니다.
	- public ApiInfo(title, description, version, termsOfServiceUrl, contact, license, licenseUrl, vendorExtensions)
    
 
3.  Swagger UI를 테스트할 수 있도록 샘플 Controller를 작성해줍니다.
    
    ```java
	    @Controller
		public class LoginController {
			@Autowired
			private PasswordEncoder passwordEncoder;

			@Autowired
			private UserService userService;

			@PostMapping("/join")
			public String join(User user) {
				user.setPassword(passwordEncoder.encode(user.getPassword()));
				userSevice.joinUser(user);
				return "redirect:/loginForm";
			}

		}
    ```
이제, 애플리케이션을 구동해 봅시다.

4.  Swagger UI 확인
[http://localhost:8080/swagger-ui/index.html](http://localhost:8080/swagger-ui/index.html) 로 접근하여, Swagger UI가 정상적으로 출력되는지 확인합니다.


![](https://lh6.googleusercontent.com/XRZffeRdQAcUicVPAI2tZeoOZkY9DEhCQLq4sc9OYJst264hQAUTlrsKq7b5loTKHZOOdkLOJ7wAfIt4UxzBYzXd3PEZ0I42NSrlP6e-spERKX0dI0nhq6OeHzWvwPG1QWbhG0xq)

  
  

![](https://lh6.googleusercontent.com/NVdnd8QhXk-QcH4U7YvDzVdcVVxCBYQSQEa1Uq1FOk473WtTQ6TcjmGFlyb-QIsdh4v8kQkF6aHpvxNfYz0L6g7SSmzTiY_-m8O7FUT4aPmpcLshuvUP6Mj9Kk81DzIjaEln9FII)

Swagger UI가 정상적으로 출력되어, 잘 구성되는 것을 확인하였습니다.
Try it out 버튼을 누르고 원하는 정보를 입력한 후 execute 해보면서 쉽고 빠르게, API를 테스트 할 수 있습니다.


## Swagger 응용 및 annotation 소개
Swagger는 보다 나은 API 문서 및 UI를 제공하기 위해, 다양한 자바 기반 애너테이션을 제공합니다. 그중 대표가되는 애너테이션을 아래에서 살펴보도록 하겠습니다. 자세한 내용은 [https://github.com/swagger-api/swagger-core/wiki/Swagger-2.X---Annotations](https://github.com/swagger-api/swagger-core/wiki/Swagger-2.X---Annotations) 을 확인하세요.

  
**@ApiOperation (value, optional)**
-   해당 API에 대한 설명을 추가할 수 있습니다.
    ```java
    @ApiOperation(value = "사용자 등록", notes = "사용자 정보를 입력하여 사용자를 생성합니다")

	@PostMapping("/join")
	public String join(User user) {
		user.setPassword(passwordEncoder.encode(user.getPassword()));
		userService.joinUser(user);
		return "redirect:/loginForm";
	}
    ```

필수 요소
-   value - 작업에 대한 요약입니다.
    
선택 요소
-   notes - 작업에 대한 자세한 설명입니다.
-   tags - API 문서 제어를 위한 태그 목록입니다.
    

  

![](https://lh4.googleusercontent.com/kJI9gH_qVGDtgoNG2jcxSAo8xC6D4yZd-s6QhoN1UZEFL0BSaq0_xyFpI2BV04HQ7jlbzJHB-E7r9FT8zSc8uTbiPiYFBDjlaLcuD8eMdxfjdEwOc5pRJhYpCVAUbvOHE-7w1US7)

  

**@ApiImplicitParam (optional)**
-   해당 API 호출에 필요한 Parameter 들의 설명을 추가할 수 있습니다.
	```java
	@ApiOperation(value = "사용자 등록", notes = "사용자 정보를 입력하여 사용자를 생성합니다")
	@ApiImplicitParam(name = "id", value = "사용자 아이디"
						, required = true
						, dataType = "string"
						, paramType = "path"
						, defaultValue = "None")
	@PostMapping("/join")
	public String join(User user) {
		user.setPassword(passwordEncoder.encode(user.getPassword()));
		userService.joinUser(user);
		return "redirect:/loginForm";
	}
	```    
-   여러개의 파라미터에 대한 설명을 추가하고 싶은 경우 @ApiImplicitParams를 사용하면 됩니다.
    ```java
    @ApiOperation(value = "사용자 등록", notes = "사용자 정보를 입력하여 사용자를 생성합니다")
	@ApiImplicitParams({
		@ApiImplicitParam(name = "id", value = "사용자 아이디"),
		@ApiImplicitParam(name = "email", value = "사용자 이메일")
	})
	@PostMapping("/join")
	public String join(User user) {
		user.setPassword(passwordEncoder.encode(user.getPassword()));
		userService.joinUser(user);
		return "redirect:/loginForm";
	}
    ```

![](https://lh4.googleusercontent.com/jiEm4y51_0CW-GQzI4wwMEnq2c2JuSc31_y37nLCCDhTf9ZlAHO1vQSeROUiyqkWniATkYfJcHRjd-LITkD2yOOL93LbHbXd7CeEWky33-I8CpJcZp-QvCa3zDV5tgVeUxOo79ho)

  
선택요소

-   name - 파라미터의 이름입니다.
-   value - 파라미터의 간략한 설명입니다.
-   required - 파라미터가 필수인지 여부를 지정해줍니다.
-   dataType - 파라미터의 데이터 타입을 명시해줍니다.
-   paramType - 파라미터의 파라미터 타입을 명시해줍니다.
	-   @RequestParam일 경우 : query
	-   @PathVariable일 경우 : path
-   defaultValue - 파라미터의 기본값을 설명합니다.
    

@ApiResponse (code, message)
-   해당 method의 Response에 대한 설명을 작성할 수 있습니다.
    ```java
    @GetMapping("/board/list")
	@ResponseBody
	@ApiResponse(code = 200, message = "성공입니다")
	public List<Board> getBoardList(){
		return boardService.getBoardList();
	}
    ```

![](https://lh4.googleusercontent.com/eiWYwWF_3-cZ69XRo1bBGyuHs6mo5ElxXKevCObgOiLdSdvixgT6kn_lv2kwMA-Q4hEJ-HVqhlJvWkePfgooHGDszaXpHBvT-97wruPQ9CXEUds5kjE3eE08WgNdzPiJGvCkxXXP)

**필수 요소**
-   code - HTTP 응답 상태 코드입니다.
-   message - 응답과 함께 사용자에게 보여줄 메시지입니다.
    

-   여러개의 Response에 대한 설명을 추가하고 싶은 경우 @ApiReponses를 사용하면 됩니다.
	```java
	@GetMapping("/board/list")
	@ResponseBody
	@ApiResponses({
		@ApiResponse(code = 200, message = "성공입니다"),
		@ApiResponse(code = 400, message = "실패입니다")
	})
	public List<Board> getBoardList(){
		return boardService.getBoardList();
	}
	```
  
**@ApiParam (optional)**

-   DTO의 field에 대한 설명을 추가할 수 있습니다.
   ```java
   
	@Entity(name = "user")
	@Data
	@NoArgsConstructor
	public class User {

	@Id
	@Column(name = "user_id")
	@ApiParam(value = "사용자 ID")
	private String id;

	@Column(name = "username",nullable = false)
	@ApiParam(value = "사용자 이름")
	private String username;
	...

	}
   ```


**선택 요소**
-   value - 파라미터에 대한 간략한 설명입니다.
    
![](https://lh4.googleusercontent.com/KJlHHx7d9B-g7wvNb_YaCgbTI78iA51Bi1ncwE7F_4j6nwym3R32cFMecvNZbV6T6mr2zH5tnvr_OOgJYUtC1Y64I0AiAO0KU9Lxx9CSQAt5yrMdIJYlRWq6cNy1OTIM3JnMltaM)

  
  

[https://swagger.io/docs/specification/about/](https://swagger.io/docs/specification/about/)

[https://velog.io/@banjjoknim/Swagger](https://velog.io/@banjjoknim/Swagger)

[https://sanghaklee.tistory.com/50](https://sanghaklee.tistory.com/50)

[https://log-laboratory.tistory.com/347](https://log-laboratory.tistory.com/347)

[https://m.blog.naver.com/ndskr/222040308809](https://m.blog.naver.com/ndskr/222040308809)

[https://velog.io/@gillog/Swagger-UI-Annotation-%EC%84%A4%EB%AA%85](https://velog.io/@gillog/Swagger-UI-Annotation-%EC%84%A4%EB%AA%85)
