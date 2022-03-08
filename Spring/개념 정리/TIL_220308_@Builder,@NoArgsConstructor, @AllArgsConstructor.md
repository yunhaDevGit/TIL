# @Builder 사용, @NoArgsConstructor, @AllArgsConstructor

아래는 Lombok의 @Builder를 사용할 때 종종 발생하는 에러이다.

![image](https://user-images.githubusercontent.com/74949294/157149255-5da9d06b-9914-49aa-8043-ebb00e496ec4.png)

해당 DTO는 아래와 작성을 했고 실행했을 경우 위와 같은 에러가 발생했다. 

```java
@Getter
@NoArgsConstructor
@Builder
@ToString
public class HealthCheckResponseDto {

  @Schema(description = "아이디")
  private String id;
  @Schema(description = "리스너 아이디")
  private String listenerId;
  @Schema(description = "모니터 타입")
  private String monitorType;
}
```

### 원인

원인은 @Builder와 @NoArgsConstructor를 함께 사용해줄 때, @AllArgsConstructor 어노테이션이나 전체 멤버 변수를 갖는 생성자를 작성해주지 않아서였다. 

빌더 패턴의 의미상 아무것도 없는 생성자는 필요가 없다. 

빌더 패턴의 기본은 필드의 초기화 작업을 좀 더 유연하게 도와주는 역할을 하기 때문이다. 

> ***Lombok 공식사이트 @Builder 설명 中***
> Finally, applying @Builder to a class is as if you added @AllArgsConstructor(access = AccessLevel.PACKAGE) to the class and applied the @Builder annotation to this all-args-constructor. This only works if you haven't written any explicit constructors yourself.
 

또한 @Builder를 사용할 경우, 생성한 생성자가 없다면 @AllArgsConsturctor(access=AcessLevel.PACAKAGE)가 암묵적으로 적용 된다.

반대로 말하면 생성자가 있을 경우, @AllArgsConstructor가 적용되지 않는다는 것 같다.

[https://yuja-kong.tistory.com/99](https://yuja-kong.tistory.com/99)
