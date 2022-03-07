# DTO의 @Builder, @RequestBody와 생성자

모든 사용자 요청을 주고 받을 때 사용하는 DTO 클래스를 각각 역할 별로 나누어서 작성하였다. 

아래는 로드 밸런서 생성 시 @RequestBody로 넘어올 객체를 정의한 클래스이다. 

```java
@Getter
@Schema(description = "로드 밸런서 생성을 위한 객체")
//@NoArgsConstructor
public class CreateLoadbalancerRequestDto implements Serializable {

  @Schema(description = "이름")
  private String name;
  @Schema(description = "상태")
  private COMMON_STATE state;
  @Schema(description = "아이피")
  private String ip;
  @Schema(description = "라우터 아이디")
  private String routerId;
  @Schema(description = "설명")
  private String description;
  @Schema(description = "보호 설정")
  private int protection;
  @Schema(description = "리스너 정보")
  private ListenerDto listenerDto;
  @Schema(description = "생성자")
  private String creator;
  @Schema(description = "프로젝트 아이디")
  private String projectId;

  @Builder
  public CreateLoadbalancerRequestDto(String name, COMMON_STATE state, String ip,
      String routerId, String description, int protection,
      ListenerDto listenerDto, String creator, String projectId) {
    this.name = name;
    this.state = state;
    this.ip = ip;
    this.routerId = routerId;
    this.description = description;
    this.protection = protection;
    this.listenerDto = listenerDto;
    this.creator = creator;
    this.projectId = projectId;
  }

  @Override
  public String toString() {
    return "CreateLoadbalancerRequestDto{" +
        "name='" + name + '\'' +
        ", state=" + state +
        ", ip='" + ip + '\'' +
        ", routerId='" + routerId + '\'' +
        ", description='" + description + '\'' +
        ", protection=" + protection +
        ", listenerDto=" + listenerDto +
        ", creator='" + creator + '\'' +
        ", projectId='" + projectId + '\'' +
        '}';
  }
}
```


아래와 같이 Controller를 정의해 주었고 Swagger를 통해 API 테스트를 해보았다. 

```java
@Tag(name = "Loadbalancer Controller")
@RestController
@RequestMapping("/loadbalancers")
@RequiredArgsConstructor
public class LoadbalancerController {

  private final LoadbalancerService loadbalancerService;

  @Operation(summary = "로드 밸런서 생성")
  @PostMapping
  public ResponseEntity<?> createLoadbalancer(@RequestBody CreateLoadbalancerRequestDto createLoadbalancerRequestDto) {
    LoadbalancerResponseDto loadbalancerResponseDto = loadbalancerService.create(createLoadbalancerRequestDto);
    return ResponseEntity.created(URI.create("/loadbalancers")).body(loadbalancerResponseDto);
  }
}
```
![image](https://user-images.githubusercontent.com/74949294/156974789-1292d305-0335-4865-ab69-8713df23b219.png)

해당 API를 테스트 했을 때, 아래와 같은 메시지와 함께 에러가 발생했다. 

```powershell
2022-03-07 12:58:45.346 ERROR 9225 --- [nio-9696-exec-4] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed; nested exception is org.springframework.http.converter.HttpMessageConversionException: Type definition error: [simple type, class com.innogrid.cloudit6.domain.network.loadbalancer.dto.CreateLoadbalancerRequestDto]; nested exception is com.fasterxml.jackson.databind.exc.InvalidDefinitionException: Cannot construct instance of `com.innogrid.cloudit6.domain.network.loadbalancer.dto.CreateLoadbalancerRequestDto` (no Creators, like default constructor, exist): cannot deserialize from Object value (no delegate- or property-based Creator)
 at [Source: (org.springframework.util.StreamUtils$NonClosingInputStream); line: 2, column: 3]] with root cause

com.fasterxml.jackson.databind.exc.InvalidDefinitionException: Cannot construct instance of `com.innogrid.cloudit6.domain.network.loadbalancer.dto.CreateLoadbalancerRequestDto` (no Creators, like default constructor, exist): cannot deserialize from Object value (no delegate- or property-based Creator)
 at [Source: (org.springframework.util.StreamUtils$NonClosingInputStream); line: 2, column: 3]
	at com.fasterxml.jackson.databind.exc.InvalidDefinitionException.from(InvalidDefinitionException.java:67) ~[jackson-databind-2.13.1.jar:2.13.1]
	at com.fasterxml.jackson.databind.DeserializationContext.reportBadDefinition(DeserializationContext.java:1904) ~[jackson-databind-2.13.1.jar:2.13.1]
	at com.fasterxml.jackson.databind.DatabindContext.reportBadDefinition(DatabindContext.java:400) ~[jackson-databind-2.13.1.jar:2.13.1]
	...
```

기본 생성자가 없어서 발생하는 문제였는데, @Builder 어노테이션을 썼을 경우 당연히 기본 생성자가 생기는 줄 알았는데 그게 아니었나보다...(요건 조금 더 찾아봐야지)

하여튼 @NoArgsController를 추가해주었더니 아래와 같이 요청이 잘 넘어오는 것을 확인할 수 있었다. 
![image](https://user-images.githubusercontent.com/74949294/156974749-6af62c39-0d20-4ed2-995c-fa582c3ed471.png)

그런데 한 가지 이상했던 점이 @Builder 패턴에 대해 알아보던 중 @Builder 패턴을 쓸 경우에는 @NoArgsConstructor와 @AllArgsConstructor를 함께 써주어야 한다고 하는 글을 여러 개 보았다. 

그러나 나는 @AllArgsConstructor가 없이도 에러 없이 정상적으로 동작을 하였고 값도 제대로 잘 넘어왔다. 🧐

호기심에 @Builder가 적용된 생성자의 몇몇 파라미터를 지우고 실행해보았다. 

```java
  ...
  @Builder
  public CreateLoadbalancerRequestDto(String name, COMMON_STATE state, String description, int protection,
      ListenerDto listenerDto, String creator, String projectId) {
    this.name = name;
    this.state = state;
		// this.routerId = routerId;
    // this.description = description;
    this.description = description;
    this.protection = protection;
    this.listenerDto = listenerDto;
    this.creator = creator;
    this.projectId = projectId;
  }
  ...
```

...

결과는 똑같았다... ☹️

흠...나는 Controller가 Client 요청을 받았을 때 RequestBody의 데이터들은 RequestBody를 담는 객체의 생성자를 통해 생성 된다고 생각했는데 아닌가...??? 🧐

결론은 아니었다!!

파라미터에 `@RequestBody` 가 있으면 `HttpMessageConverter`를 통해 HTTP body를 매핑해준다고 한다.

`HttpMessageConverter`는 HTTP request message를 역직렬화(Object로 파싱) 해준다고 한다.
