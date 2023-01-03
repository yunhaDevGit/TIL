# @Primary

## @Primary?

- 우선 순위를 부여하고자 할 때 사용하는 어노테이션입니다.
- 동일한 유형의 빈이 여러 개 있을 때 빈에 더 높은 우선 순위를 부여합니다.

## 사용 예시

`@Primary` 어노테이션은 아래와 같이 동일한 유형의 빈에 액세스 하고자 할 때 사용합니다. 

```java
@Configuration
public class Config {
	
	@Bean
	public Employee JohnEmployee() {
		return new Employee("John");
	}

	@Bean
	public Employee TonyEmployee() {
		return new Employee("Tony");
	}
}
```

위의 코드를 실행 시킬 경우 **NoUniqueBeanDefinitionException**을 던집니다. 

```java
@Configuration
public class Config {
	
	@Bean
	public Employee JohnEmployee() {
		return new Employee("John");
	}

	@Bean
	@Primary
	public Employee TonyEmployee() {
		return new Employee("Tony");
	}
}
```

그러나 `**@Primary` 표시를 할 경우, Spring은 JohnEmployee() 보다  TonyEmployee() 빈을 먼저 주입**힙니다.
