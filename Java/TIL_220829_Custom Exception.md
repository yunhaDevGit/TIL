# Custom Exception 만들기

## Custom Exception의 필요성

Java는 프로그래밍에서 발생하는 거의 모든 일반적인 예외를 제공합니다.

그러나 표준 예외에 모든 예외가 포함될 수 없기 때문에 이를 보완하기 위해 Custom Exception을 만들어 사용할 수 있습니다. 

```bash
**일반 예외**로 선언할 경우 **Exception**을 상속받아 구현
**실행 예외**로 선언할 경우 **RuntimeException**을 상속받아 구현
```

- 사용자 정의 예외 클래스는 컴파일러가 체크하는 **일반 예외**로 선언할 수도 있고, 컴파일러가 체크하지 않는 **실행 예외**로 선언할 수도 있습니다.
- 일반 예외로 선언할 경우 **Exception**을 상속하면 되고, 실행 예외로 선언할 경우에는 **RuntimeException**을 상속하면 됩니다.
- 사용자 정의 예외 클래스 이름은 Exception으로 끝나는 것을 권장합니다.
- 사용자 정의 예외 클래스 작성 시 생성자는 두 개를 선언하는 것이 일반적입니다.
    - 매개 변수가 없는 기본 생성자
    - 예외 발생 원인(예외 메시지)을 전달하기 위해 String 타입의 매개변수를 갖는 생성자
- 예외 메시지의 용도는 catch {} 블록의 예외처리 코드에서 이용하기 위해서입니다.

## RuntimeException 예시

```java
public class CustomException extends RuntimeException {

    // 1. 매개 변수가 없는 기본 생성자
    CustomException() {

    }

    // 2. 예외 발생 원인(예외 메시지)을 전달하기 위해 String 타입의 매개변수를 갖는 생성자
    CustomException(String message) {
        super(message); // RuntimeException 클래스의 생성자를 호출합니다.
    }
}
```
