
# @RequsetBody 파싱 에러

### 문제

- postman을 사용하여 아래와 같이 request를 했는데 아래 이미지와 같이  null 값으로 나오는 문제가 발생했다.

```json
{
    "name": "test_vm",
    "cpuNum": 8,
    "memSize": 2,
    "vncPort": 44
}
```

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/b951f9f2-f6a0-4f8c-8b75-fbc08da91436/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211117%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211117T034648Z&X-Amz-Expires=86400&X-Amz-Signature=76bc54349117e8c2b561caa5b19b64e011cadb70d07321da2943f5636f24d8cf&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

DTO 클래스와 Controller는 각각 아래와 같이 만들었다.

DTO

```java
@Getter
@Setter
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class Vm {

  @Id
  @GeneratedValue(generator = "uuid")
  @GenericGenerator(name="uuid", strategy = "uuid2")
  @Column(columnDefinition = "VARCHAR(36)", insertable = false, updatable = false, nullable = false)
  private String id;
  private String name;

  private int cpuNum;
  private long memSize;
  private int vncPort;

}
```

Controller

```java
@RestController
public class VmController {

  @Autowired
  VmService vmService;

  @PostMapping("/vm/create")
  public String createVm(@RequestBody Vm vm) {
    System.out.println("VmController - createVm");
    return vmService.createVm(vm);
  }
}
```

### 원인

원인은 DTO에서 @Getter, @Setter annotation을 사용했기 때문이었다. 해당 annotation 사용 시 lombok에 의해 자동으로 getter/setter 메서드가 생성되는데 그 과정에서 각 요소를 인식하지 못했기 때문이다.

나와 같이 @RequestBody로 넘어오는 값이 null인 경우는 많았으나 대체로 원인은 필드명이 첫 번째, 또는 두 번째 문자까지 대문자인 경우 lombok에 의해 자동으로 생성되는 getter 메서드가 앞의 대문자를 소문자로 임의로 변경하면서 발생한 문제였다.

하지만 나의 필드명은 소문자로 시작했기 때문에 해당 문제와는 조금 다른 것 같았다. 

위의 문제일 경우 정확한 원인과 해결 방법은 아래 링크에서 확인할 수 있다.

[https://darkstart.tistory.com/299](https://darkstart.tistory.com/299)

[https://the-dev.tistory.com/21](https://the-dev.tistory.com/21)

[https://bcp0109.tistory.com/309](https://bcp0109.tistory.com/309)

정확한 원인 파악은 못했지만 위의 문제들도 직접 getter/setter를 만들어 주었을 경우 해결되었다길래 나도 getter/setter 메서드를 직접 만드니 정상적으로 값이 들어왔다.

(@RequestBody 할 경우 setter는 필요하지 않기 때문에 만들어주지 않았다)

```java
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class Vm {

  @Id
  @GeneratedValue(generator = "uuid")
  @GenericGenerator(name="uuid", strategy = "uuid2")
  @Column(columnDefinition = "VARCHAR(36)", insertable = false, updatable = false, nullable = false)
  private String id;
  private String name;

  private int cpuNum;
  private long memSize;
  private int vncPort;

  public String getId() {
    return id;
  }

  public String getName() {
    return name;
  }

  public int getCpuNum() {
    return cpuNum;
  }

  public long getMemSize() {
    return memSize;
  }

  public int getVncPort() {
    return vncPort;
  }
}
```

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/3a0035da-1882-432e-bde3-ceeb86d1965f/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211117%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211117T034734Z&X-Amz-Expires=86400&X-Amz-Signature=a8eeb1b3b6b40e53945340c271b19dd8ee2e43daf7f8d150397390b7ae214157&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

아래와 같이 정상적으로 값이 넘어오는 것을 확인할 수 있었다. 

근데 진짜 근본적인 원인을 잘 모르겠다ㅠㅠㅠ 아니면 정말 @Getter annotation 문제 일 수도 있지만....확실하게 알진 못했다

---

## 해결 !!

원인 파악을 계속 못하고 있던 중 회사 선배가 같이 봐주셨다~

@Entity 문제인가 싶어 Dto 클래스를 새로 만든 후 시도도 해보았고 ide cache도 날리고, build도 해보았지만 여전히 동일한 문제가 발생했다. 

그러다가 설마 하고 build.gradle을 보시곤 lombok dependency 추가할 때 잘못 된 것 같다고 하고 아래와 같이 바꿔주셨다.

```java
// 기존에 작성되었던 build.gradle
implementation 'org.projectlombok:lombok:1.18.20'

// 변경된 build.gradle
compileOnly 'org.projectlombok:lombok'
annotationProcessor 'org.projectlombok:lombok'
```

위와 같이 build.gradle을 작성해주니 정상적으로 값이 들어왔다.

annotationProcessor를 추가해주지 않았기 때문이었다.

혹시 싶어 lombok 버전 문제인가 확인도 해봤지만 버전 문제가 아닌 annotationProcessor 때문이었다. 

간단히 말하자면 annotationProcessor는 compile 시점에 annotation으로 작성된 것들을 코드로 변경해주는 작업을 해주는 건데 예를 들면, @Getter를 사용할 때 getOO 으로 변경해주는 작업이다. 

lombok을 사용하면서 @Getter/@Setter를 쓰면서 해당 dependency를 작성해주지 않아 정상적으로 동작하지 않았던 거였다.

나중에 이 부분에 대해서는 좀 더 자세히 찾아봐야겠다
