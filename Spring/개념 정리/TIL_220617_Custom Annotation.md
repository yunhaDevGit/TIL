# Custom Annotation

Spring에서 bean을 만드는 방법은 여러가지 있지만, 그 중 @Component 어노테이션을 이용하는 방법이 있습니다. 

@Component 어노테이션이 어떻게 만들어지고, 어떻게 동작하는지 보곘습니다.

**@Component 어노테이션**

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Indexed
public @interface Component {
	String value() default "";
}
```

Component @Traget, @Retention, @Documented 총 3개의 어노테이션과 @Indexed 어노테이션을 갖고 있습니다. 

J3SE 5.0부터 어노테이션을 작성할 때 주로 java.lang.annotation 패키지에 있는 4가지 어노테이션을 이용하여 작성합니다. 

- `@Documented` : Java doc에 문서화 여부를 결정합니다.
- `@Retention` : 어노테이션의 지속 시간을 정합니다.
    - RetentionPolicy.SOURCE : 컴파일 후 정보들이 사라집니다. 이 어노테이션은 컴파일이 완료된 후에는 의미가 없으므로, 바이트 코드에 기록되지 않습니다. 예시로는 @Override와 @SuppressWarnings 어노테이션이 있습니다.
    - RetentionPolicy.CLASS : default 값입니다. **컴파일 타임** 때만 .class 파일에 존재하며, **런타임** 때는 없어집니다. 바이트 코드 레벨에서 어떤 작업을 해야 할 때 유용합니다. Reflection 사용이 불가능 합니다.
    - RetentionPolicy.RUNTIME : 이 어노테이션은 런타임시에도 .class 파일에 존재합니다. 커스텀 어노테이션을 만들 때 주로 사용합니다. Reflection 사용 가능합니다.
- `@Target` : 어노테이션을 작성할 곳입니다. **default 값은 모든 대상입니다.** 예를 들어 **@Target(ElementType.FIELD)**로 지정해주면, 필드에만 어노테이션을 달 수 있습니다. 만약 필드 말고 다른 부분에 어노테이션을 사용한다면 컴파일 때 에러가 나게 됩니다.
    - ElementType.TYPE (class, interface, enum)
    - ElementType.FIELD (instance variable)
    - ElementType.METHOD
    - ElementType.PARAMETER
    - ElementType.CONSTRUCTOR
    - ElementType.LOCAL_VARIABLE
    - ElementType.ANNOTATION_TYPE (on another annotation)
    - ElementType.PACKAGE (remember package-info.java)
- `@Inherited` : 자식 클래스에 상속할 지 결정

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5ccbf2b0-bef1-4508-9ef3-cf1c46a963ed/Untitled.png)

## Custom 어노테이션 만들기

1. 어노테이션 타입은 **@interface**로 정의해야 합니다. 모든 어노테이션은 자동적으로 `java.lang.Annotation` **인터페이스를 상속하기 때문에** 다른 클래스나 인터페이스를 상속 받으면 안됩니다.
2. 파라미터 멤버들의 접근자는 public이거나 default 여야만 합니다.
3. 파라미터 멤버들은 byte, short, char, int, float, double, boolean의 기본 타입과 String, Enum, Class, 어노테이션만 사용할 수 있습니다.
4. 클래스 메소드와 필드에 관한 어노테이션 정보를 얻고 싶으면, **reflection**만 이용해서 얻을 수 있습니다. 다른 방법으로는 어노테이션 객체를 얻을 수 없습니다. 

### **Example**

FruitColor(과일에 색을 지정해주는 어노테이션)을 만들어 보겠습니다. 

```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface FruitColor {
	enum Color{BLUE, RED, GREEN}

	Color fruitColor() default Color.GREEN;
}
```

만약 FruitColor 어노테이션의 Color 값을 주지 않으면 default로 GREEN이 사용 됩니다.

### Spring에서 어노테이션 사용 방법

Spring에서 애플리케이션 실행 시 @Service나 @Component가 붙은 클래스들을 스캔해서 IoC 컨테이너에 등록해주는 과정이 있습니다. 

**@Service 어노테이션**

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Service {
    @AliasFor(
        annotation = Component.class
    )
    String value() default "";
}
```

Service 어노테이션은 Target이 TYPE으로 지정되어 있습니다. **Class나 Interface를 타겟**으로 삼는다는 의미입니다.

또한 같은 패키지인 `org.springframework.stereotype`의 **Component 어노테이션**을 쓰고 있는 걸 볼 수 있습니다.

[https://donghyeon.dev/spring/2020/08/18/Spring-Annotation의-원리와-Custom-Annotation-만들어보기/](https://donghyeon.dev/spring/2020/08/18/Spring-Annotation%EC%9D%98-%EC%9B%90%EB%A6%AC%EC%99%80-Custom-Annotation-%EB%A7%8C%EB%93%A4%EC%96%B4%EB%B3%B4%EA%B8%B0/)
