# Serializable

### Java 직렬화

- 자바 시스템 내부에서 사용되는 객체 또는 데이터들을 외부의 자바 시스템에서도 사용할 수 있도록 `바이트(byte)` 형태로 데이터 변환하는 기술과 바이트로 변환된 데이터를 다시 객체로 변환하는 역직렬화를 포함
- 시스템적으로 JVM의 Runtime Data Area에 상주하고 있는 객체 데이터를 바이트 형태로 변환하는 기술과 직렬화 된 바이트 형태의 데이터를 객체로 변환해서 JVM으로 상주 시키는 형태

### 역직렬화 조건

직렬화 대상이 된 객체의 클래스가 class path에 존재해야 하며 import 되어 있어야 한다. 

- **중요한 점은 직렬화와 역직렬화를 진행하는 시스템이 서로 다를 수 있다는 것을 고려해야 한다.**

자바 직렬화 대상 객체는 동일한 `serialVersionUID` 를 가지고 있어야 한다. 

### 직렬화 사용 경우

- JVM의 메모리에서 상주하는 객체 데이터를 그대로 영속화 할 때 사용한다.
    - 시스템이 종료되더라도 사라지지 않으며, 영속화된 데이터이기 때문에 네트워크로 전송도 가능하다.
- Servlet Session
    - Servlet 기반의  WAS들은 대부분 세션의 Java 직렬화를 지원
- Cache
    - 캐시할 부분을 직렬화된 데이터를 저장해서 사용
- Java RMI(Remote Method Invocation)
    - 원격 시스템의 메서드를 호출할 때 전달하는 메시지(객체)를 직렬화 하여 사용
    - 메시지(객체)를 전달 받은 원격 시스템에서는 메시지(객체)를 역직렬화 하여 사용
- 객체가 세션에 저장하지 않는 단순한 데이터 집합이고, 컨트롤러에서 생성 되어서 뷰에서 소멸하는 데이터의 전달체라면 객체 직렬화는 고려하지 않아도 된다.
- 세션 관리를 스토리지나 네트워크 자원을 사용한다면 객체 직렬화를 해야 하고, 메모리에서만 관리한다면 객체 직렬화를 할 필요가 없다. 둘 다 고려할 경우에는 직렬화가 필요하다.

### serialVersionUID 사용 시 주의할 점

serialVersionUID는 직렬화 및 역직렬화 할 때 필요한 버전 정보이다. 

객체를 직렬화하고 클래스에 멤버변수가 추가된다면 `java.io.InvalidClassException` 예외가 발생한다. 

- serialVersionUID는 필수 값이 아니다
- 호환 가능한 클래스는 serialVersionUID 값이 고정되어 있다
- serialVersionUID가 선언되어 있지 않으면 클래스의 기본 해쉬값을 사용한다

### 직렬화의 조건

- `Java.io.Serializable` 인터페이스를 상속 받은 객체와 Primitive 타입의 데이터가 직렬화 대상이 될 수 있다.
    - 기본 자료형(Primitive Type)은 정해진 Byte의 변수이기 때문에 Byte 단위로 변환하는 것에 문제가 없지만, 객체의 크기는 가변적이며, 객체를 구성하는 자료형들의 종류와 수에 따라 객체의 크기가 다양하게 바뀔 수 있기 때문에 객체를 직렬화하기 위해 Serializable 인터페이스를 구현해야 한다.
- 객체의 멤버들 중 Serializable 인터페이스가 구현되지 않은 것이 존재하면 안된다.
- `Transient`가 선언된 멤버는 전송되지 않는다.
    - 객체 내에 Serializable 인터페이스가 구현되지 않은 멤버 때문에 `NonSerializableException` 이 발생하는 경우, Transient를 선언해주면 직렬화 대상에서 제외되기 때문에 문제 없이 해당 객체를 직렬화 할 수 있다.

### 실무 사용 조언

1. `serialVersionUID` 는 개발 시 직접 관리
2. 역직렬화 대상 클래스의 멤버 변수 타입 변경 지양
3. 외부(DB, 캐시 서버, NoSQL 서버 등)에 장기간 저장될 정보는 Java 직렬화 사용 지양 (클래스 변경을 예측할 수 없으므로)
4. 개발자가 직접 컨트롤 할 수 없는 클래스(프레임워크 또는 라이브러리에서 제공하는 클래스)는 직렬화 지양
5. 자주 변경되는 클래스는 Java 직렬화를 사용하지 않는 것이 좋다
6. 역직렬화에 실패하는 상황에 대한 예외 처리 필수
7. 직렬화 된 데이터는 타입 정보 등의 클래스 메타 정보를 포함하기 때문에 JSON 포맷에 비해 약 2~10배 더 사이즈가 크다. 특히 직렬화 된 데이터를 메모리 서버(Redis, Memcached)에 저장하는 환경에서 트래픽에 따라 네트워크 비용과 캐시 서버 비용이 급증할 수 있으므로, JSON 포맷으로의 변경을 고려해야 한다. 

[https://velog.io/@sa1341/Java-직렬화를-하는-이유가-무엇일까](https://velog.io/@sa1341/Java-%EC%A7%81%EB%A0%AC%ED%99%94%EB%A5%BC-%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0%EA%B0%80-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C)

[https://ryan-han.com/post/java/serialization/](https://ryan-han.com/post/java/serialization/)

[https://techblog.woowahan.com/2550/](https://techblog.woowahan.com/2550/)

[https://techblog.woowahan.com/2551/](https://techblog.woowahan.com/2551/)

[https://ryan-han.com/post/java/serialization/](https://ryan-han.com/post/java/serialization/)
