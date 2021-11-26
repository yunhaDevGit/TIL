# Lombok annotation

## @Getter / @Setter

기본 getter/setter 생성

lombok 코드

```java
@Getter
@Setter
public class Example {
	private String name;
}
```

순수 자바 코드

```java
public class Example {
	private String name;

	public setName(String name) {
		this.name = name;
	}

	public getName() {
		return name;
	}
}
```

만약 특정 필드에서 getter와 setter 생성을 하지 않고 싶을 경우 AccessLevel.None을 사용하여 막을 수 있습니다. 

lombok 코드

```java
@Getter
@Setter
public class Example {
	private String name;
	
	@Getter(AccessLevel.NONE)
	private String email;
}
```

순수 자바 코드

```java
public class Example {
	private String name;
	private String email;

	public setName(String name) {
		this.name = name;
	}

	public getName() {
		return name;
	}

	public setEmail(String email) {
		this.email = email;
	}
}
```

## **@NoArgsConstructor**

파라미터가 없는 생성자를 생성합니다. 

lombok 코드

```java
@NoArgsConstructor
public class Example {
	private String name;
}
```

순수 자바 코드

```java
public class Example {
	private String name;

	public Example() {
		...
	}
}
```

[https://dingue.tistory.com/14](https://dingue.tistory.com/14)
