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
