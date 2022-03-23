 # Spring Data JPA를 쓰면서 헷갈렸던 부분

![image](https://user-images.githubusercontent.com/74949294/159618374-a86aed70-8bf1-4fee-9ffa-fadb453ca1e1.png)


**각 객체 간의 관계**

라우터와 서브넷이 있고 각 서브넷 간에 통신하기 위해서는 라우터에 연결되어야 한다. 

서브넷은 여러 라우터에 연결 될 수 있으며, 라우터와 연결할 때, 서브넷 대역 범위 내의 유효한 IP 중 하나를 할당하고 그것을 라우터 포트라고 부른다. 

라우터 포트는 하나의 서브넷에 대응되기 때문에 1:1 관계라고 생각했고, 

서브넷에는 여러 라우터 포트가 있을 수 있기 때문에 1:n 관계라고 생각했다. 

때문에 라우터 포트의 입장에서 서브넷과의 연관 관계를 표현할 때에는 `@OneToOne`으로 표현해야 하며, 서브넷의 입장에서는 `@OneToMany`로 표현하면 되지만 나의 경우 서브넷에서는 참조를 할 필요가 없다고 느껴 라우터 포트에서는 `@OneToOne`으로 쓰면 되겠다!라고 생각을 했다. 

### 처음에 생각했던 구조

```java
@Getter
@DynamicInsert
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@AllArgsConstructor
@ToString
@Builder
@Entity(name = "router_port")
public class RouterPort extends BaseTimeEntity {

  @Id
  @GeneratedValue(generator = "uuid")
  @GenericGenerator(name = "uuid", strategy = "uuid2")
  @Column(name = "id", columnDefinition = "VARCHAR(36)", insertable = false, updatable = false, nullable = false)
  private String id;

  @OneToOne
  @JoinColumn(name = "subnet_id")
  private Subnet subnet;

  @OneToOne
  @JoinColumn(name = "router_id")
  private Router router;
	
	...(생략)
}
```

```java
@Getter
@DynamicInsert
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@AllArgsConstructor
@ToString
@Builder
@Entity(name = "subnet")
public class Subnet extends BaseTimeEntity {

  @Id
  @GeneratedValue(generator = "uuid")
  @GenericGenerator(name = "uuid", strategy = "uuid2")
  @Column(name = "id", columnDefinition = "VARCHAR(36)", insertable = false, updatable = false, nullable = false)
  private String id;

  @Column(name = "name")
  private String name;

	// 서브넷 입장에서의 라우터 포트와의 연관 관계는 굳이 필요 없어서 생략(만일 쓴다면 이런식으로 작성)
  // @OneToMany
  // @JoinColumn(name = "router_port_id")
  // private RouterPort router;

	...(생략)
}
```

이렇게 생각을 해보니 아무리 객체의 연관 관계여도 라우터 포트의 입장에서는 1:1, 서브넷 입장에서는 1:N 연관 관계를 갖는 것이 이상하다고 생각했다. 

역시 찾아보니 1:1의 경우 반대의 경우(상대편 객체)에서도 1:1이고 1:N의 경우 반대의 경우에서는 N:1이었다. 

처음에는 각 라우터 포트가 하나의 서브넷에 매핑 되기 때문에 1:1이라고 생각을 했는데 결국엔 여러 라우터 포트가 하나의 서브넷과 연관이 있기 때문에 N:1이 맞다.

JPA 공부를 더 해야겠다...ㅎㅎ

[https://ttl-blog.tistory.com/129](https://ttl-blog.tistory.com/129)

[https://sun-22.tistory.com/69](https://sun-22.tistory.com/69)
