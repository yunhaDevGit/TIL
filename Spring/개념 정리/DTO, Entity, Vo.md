### VO (Value Object)
- 값을 담는 객체
- 불변성. 상태 변경 행위 일어나면 안된다
- Read-Only
- 객체의 필드 값이 같으면, 같은 객체라고 판단
- equals(), hashcode()를 오버라이딩 하는 것이 핵심 역할

### DTO (Data Transfer Object)
- 데이터 전송 객체 (View와 통신하기 위한 클래스)
- 비동기 처리 등 값을 주고 받을 때 사용
- 필요한 속성들만 추려서 JSON 형식으로 보내줘야 할 경우, 데이터 가공을 위해 DTO 만들어준다
- 로직을 갖고 있지 않고, DB로부터 받을 데이터들을 어떤 방식, 타입으로 받거나 보낼 것인지 정의 해 놓은 양식 또는 순수한 데이터 객체 정도로 생각하면 된다.
- setter 대신, 생성자로 값을 할당
- 서비스 단에서는 dto가 노출되지 않아야 한다

```java
@Getter
public class UserDto {
	public String userId;
	public String paasword;

	public SignInReq(String userId, String password) {
		this.userId = userId;
		this.password = password;
	}
}
```

> **DTO와 VO와의 차이**
> 
> VO(Value Object)도 DTO와 동일한 개념이다. 
> 
> 다만 DTO와의 차이는, DTO는 데이터를 계층간 교환(Transfer)하는데 의미가 있고, VO는 읽기만 가능한 read-only 속성을 가진 객체로서 데이터 그 자체에 의미를 두고 있다는 점이다.
>

### Entity =  Domain
- DB 테이블의 칼럼 만을 필드로 가지는 클래스
	- Entity는 DB Layer를 위한, DTO는 View Layer를 위한 것
- 상속 받거나 구현체이면 안됨
- 최대한 Entity 클래스의 Setter를 사용하지 않도록, 필요한 로직 구현
- 구현한 로직은 주로 Service Layer에서 사용
- Presentation Logic은 들어가지 않도록 해야함
- 필드 입력에 대한 유효성 검증 등 항상 완전한 상태로 모델이 만들어지도록 해야 함
- final 키워드 사용

```java
@Entity
public class Member {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private long id;
	
	private String name;
	public long getId() {
		return id;
	}
	public void setId(long id) {
		this.id = id;
	}
	...
}
```
