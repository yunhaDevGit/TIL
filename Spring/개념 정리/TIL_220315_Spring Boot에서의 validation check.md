# Spring Boot에서의 validation check

## Validation

올바르지 않은 데이터를 걸러내고 보안을 유지하기 위해 데이터 검증은 여러 계층에 걸쳐서 적용됩니다. 

Client의 데이터는 조작이 쉬울 뿐더러 모든 데이터가 정상적인 방식으로 들어오는 것은 아니기 때문에 Client Side 뿐만 아니라 Server Side에서도 데이터 유효성 검사를 해야 할 필요가 있습니다.

스프링 부트 프로젝트에서는 `@Validated`를 이용해 유효성을 검증할 수 있습니다. 

> **@Valid, @Validated 차이**
> 
> `@Valid`는 Java에서 지원해주는 어노테이션이고, `@Validated`는 Spring에서 지원해주는 어노테이션입니다. 
> 
> `@Validated`는 `@Valid`의 기능을 포함하고, 유효성 검사할 그룹을 지정할 수 있는 기능을 추가로 제공합니다.


스프링에서는 애노테이션 하나로 검증 로직을 편리하게 적용할 수 있는 Bean Validation을 제공합니다. Bean Validation은 bean 유효성 검사를 위한 명세일 뿐 동작하는 코드가 아닙니다. 그렇기 때문에 이를 구현한 코드가 필요한데 Bean Validation을 실제로 동작하도록 구현한 것이 **`Hibernate Validator`** 입니다.

> **Bean-validation** : JSR-380, 애노테이션을 이용하여  bean 유효성 검사를 위한 Java API 스펙
> 
> **Hibernate-validator** : Bean Validation을 구현한 Java API


### Spring Boot Validation 적용하는 방법

spring boot는 Bean Validation을 Validation 모듈로 사용하고 있습니다. 

기존에는 Hibernate Validator를 사용하기 위해 `spring-boo-starter-web` 의존성을 추가하면 되었는데, spring boot `2.3 version` 이상부터는 **spring-boot-starter-web** 의존성 내부에 있던 **validation**이 사라졌습니다. 

때문에 spring boot version이 2.3 이상일 경우 validation 의존성을 따로 추가해줘야 합니다. 

*Gradle 의존성*

```java
// https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-validation
implementation group: 'org.springframework.boot', name: 'spring-boot-starter-validation', version: '2.6.4'
```

*Maven 의존성*

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-validation -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
    <version>2.6.4</version>
</dependency>
```

### @Valid 개념 및 동작 원리

@Valid는 JSR-303 표준 스펙으로 **제약 조건이 부여된 객체에 대해 Bean Validator를 이용해서 검증하도록 지시하는 어노테이션**입니다.

모든 Client 요청은 디스패처 서블릿을 통해 Controller로 전달됩니다. 그리고 Controller의 메소드를 호출하는 과정에서는 메소드 값을 ArgumentResolver가 동작하는데, `@Valid` 역시 **ArgumentResolver에 의해 처리**됩니다. 

@Valid 어노테이션을 사용할 경우 유효성 검사를 진행하게 됩니다. 만약 검증에 오류가 있다면 MethodArgumentNotValidException 예외가 발생하고, 디스패처 서블릿에 기본으로 등록된 Exception Resolver인 DefaultHandlerExceptionResolver에 의해 400 BadRequest 에러가 발생하게 됩니다. 

@Valid는 기본적으로 **컨트롤러에서만 동작**하며 기본적으로 다른 계층에서는 검증이 되지 않습니다. 다른 계층에서 파라미터를 검증하기 위해서는 `@Validated`와 결합되어야 합니다. 

**예시** 

```java
@Getter
@RequiredArgsConstructor
public class UserRequestDto {

	@NotEmpty(message = "이메일은 필수 입력값입니다.")
	@Email
	private String email;

	@NotNull
	private String password;

	...
}
```

```java
@PostMapping("/user/add") 
public ResponseEntity<?> addUser(@RequestBody @Valid UserRequestDto userRequestDto) { 
	... 
}
```

### @Validated 동작원리

특정 ArgumentResolver에 의해 유효성 검사를 했던 @Valid와 달리 @Validated는 AOP 기반으로 메소드 요청을 인터셉트 하여 처리합니다. @Validated를 클래스 레벨에 선언하면 해당 클래스에 유효성 검증을 위한 인터셉터(MethodValidationInterceptor)가 등록됩니다. 그리고 해당 클래스의 메소드들이 호출될 때 AOP의 point cut으로 요청을 가로채서 인터셉터를 통해 유효성 검증을 합니다.

때문에 `@Validated`는 Controller, Service, Repository 등 계층에 무관하게 **스프링 빈이라면 유효성 검증을 진행**할 수 있습니다. 

```java
@Service 
@Validated 
public class UserService { 
	public void addUser(@Valid UserRequestDto userRequestDto) {
		... 
	} 
}
```

> @Valid와 @Validated에 의한 예외 클래스도 각각 다릅니다. 
> 
> **@Valid -** MethodArgumentNotValidException
> 
> **@Validated** - ConstraintViolationException


[https://velog.io/@_koiil/SpringBoot-Spring-Validation을-이용한-유효성-검증](https://velog.io/@_koiil/SpringBoot-Spring-Validation%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9C%A0%ED%9A%A8%EC%84%B1-%EA%B2%80%EC%A6%9D)

[https://www.popit.kr/javabean-validation과-hibernate-validator-그리고-spring-boot/](https://www.popit.kr/javabean-validation%EA%B3%BC-hibernate-validator-%EA%B7%B8%EB%A6%AC%EA%B3%A0-spring-boot/)

[https://mangkyu.tistory.com/174](https://mangkyu.tistory.com/174)
