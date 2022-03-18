# MapStruct 사용

Entity와 Dto간의 매핑을 간단하게 구현할 수 있도록 지원해주는 다양한 라이브러리가 있다.

그 중 내가 사용한 것은 MapStruct로 우리나라와 일본을 제외한 해외에서는 MapStruct가 주로 사용되고 있다고 한다. (우리나라와 일본은 ModelMapper를 많이 사용함)

ModelMapper보다 MapStruct가 비교적 빠르다고 한다.

MapStruct를 사용하기 위해 아래와 같은 설정을 해주었다.

### 1. 의존성 추가

build.gradle에 아래와 같이 dependencies를 추가해주었다.

하나라도 빠지면 안되니 주의할 것!

```java
implementation 'org.mapstruct:mapstruct'
implementation 'org.projectlombok:lombok-mapstruct-binding'
implementation "org.projectlombok:lombok"

annotationProcessor "org.mapstruct:mapstruct-processor"
annotationProcessor "org.projectlombok:lombok-mapstruct-binding"
annotationProcessor "org.projectlombok:lombok"
```

### 2. IDE 설정

아래와 같이 File → Settings →Plugins 에서 MapStruct Support 플러그인을 설치한다. 

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/af3e2db3-c6c9-4596-8d44-156b4c8543f9/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211122%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211122T085516Z&X-Amz-Expires=86400&X-Amz-Signature=2e44b917f6f1b433461de347b8d94e767e3020bc5fbd90e02360a332c96d0479&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

### 3. MapStruct 적용

MapStruct를 적용하기 위해 Generic을 사용해 미리 interface를 만든다.

```java
public interface GenericMapper<D, E> {

    D toDto(E e);
    E toEntity(D d);

    @BeanMapping(nullValuePropertyMappingStrategy = NullValuePropertyMappingStrategy.IGNORE)
    void updateFromDto(D dto, @MappingTarget E entity);
}
```

@MappingTarget : 변환하여 객체를 return 하는 것이 아닌 인자로 받아 업데이트할 target을 설정한다.

@BeanMapping(nullValuePropertyMappingStrategy = NullValuePropertyMappingStrategy.IGNORE)

- updateFromDto()는 Entity 업데이트 시 값이 null이 아닌 값만 업데이트 하도록 할 수 있습니다.

**Mapper 생성**

위에서 만든 GenericMapper를 상속 받아 생성한다

```java
@Mapper(componentModel = "spring")
public interface VmMapper extends GenericMapper<VmDto, Vm> {

}
```

### 4. 사용

```java
@Service
public class VmService {

  @Autowired
  VmRepository vmRepository;

  private final VmMapper vmMapper = Mappers.getMapper(VmMapper.class);

  ...

  @Transactional
  public String createVm(VmDto vmDto) {

    // db에 넣는 로직
    Vm vm = vmMapper.toEntity(vmDto);
    vmRepository.save(vm);
		...
	}
}
```
-----

## 다른 사용법

[https://mapstruct.org/](https://mapstruct.org/)

**Car.java**

```java
public class Car {
 
    private String make;
    private int numberOfSeats;
    private CarType type;
 
    //constructor, getters, setters etc.
}
```

**CarDto.java**

```java
public class CarDto {
 
    private String make;
    private int seatCount;
    private String type;
 
    //constructor, getters, setters etc.
}
```

**CarMapper.java**

```java
@Mapper 1
public interface CarMapper {
 
    CarMapper INSTANCE = Mappers.getMapper( CarMapper.class ); 3
 
    @Mapping(source = "numberOfSeats", target = "seatCount")
    CarDto carToCarDto(Car car); 2
}
```

1. `@Mapper` annotation은 인터페이스를 매핑 인터페이스로 표시하고 컴파일하는 동안 MapStruct 프로세서가 동작하도록 합니다. 
2. 실제 매핑 메서드는 source 객체를 매개 변수로 받고 target 객체를 반환합니다. 이름은 자유롭게 설정할 수 있습니다. 
source와 target 객체 간에 이름이 다를 경우`@Mapping` 어노테이션을 통해 설정할 수 있습니다.
필요에 따라 가능한 경우 source와 target의 유형이 다를 때 서로 다른 속성에 대해 유형 변환을 해줍니다. 
예를 들면, enum 타입을 string으로 변환해줍니다.
3. 인터페이스 구현 인스턴스는 `Mappers` 클래스에서 가져올 수 있습니다. 일반적으로 인터페이스는 멤버를 `INSTANCE`로 선언하고 클라이언트에게 mapper 구현체에 대한 접근을 제공합니다.
