# [Java] Error, Exception

## Java 예외 종류

1. Error
2. RuntimeException
3. OtherException

![image](https://user-images.githubusercontent.com/74949294/163194950-17c94217-bfbc-4935-90ed-b90d2bf2e3d7.png)

## Error

Error는 메모리 부족, 스택오버플로우처럼 **자바 가상 머신(JVM)이나 하드웨어 등 시스템의 문제로 발생하는 것**을 의미한다.

즉, 개발자가 처리할 수 있는 영역이 아니기 때문에 Error가 발생하면 프로그램을 종료시키는 것이 일반적이다. 

## Exception

Error와 Exception의 차이는 **개발자가 프로그램 내에서 처리할 수 있는지에 대한 여부**이다.  Exception은 Error와 다르게 프로그램 내에서 처리해줄 수 있다.

즉, 예외가 발생하더라도 프로그램을 비정상 종료시키지 않는다.

Exception은 **RuntimeException과 OtherException**으로 나뉜다. 이들의 구분하는 차이는 **컴파일러가 예외 처리를 하는지에 대한 여부**이다.

RuntimeException은 Error와 함께 컴파일러가 예외를 체크하지 않는다. 

→ **비체크 예외(UnCheckedExceptions)라고 한다.**

→ 꼭 예외 처리를 해줄 필요 없다.

OtherException은 Error와 RuntimeException을 제외한 모든 예외를 의미하며 **컴파일러가 예외를 체크**해 준다.

→ **체크 예외(Checked Exception)이라고 한다.** 

→ 반드시 예외 처리를 해야 한다.

**RuntimeException 종류**

| ArithmeticException | 어떤 수를 0으로 나눌 때 발생 |
| --- | --- |
| NullPointerException | Null 객체를 참조할 때 발생 |
| ClassCastException |  적절하지 못하게 클래스를 형 변환하는 경우 |
| NegativeArraySizeException | 배열의 크기가 음수 값인 경우 |
| OutOfMemoryException | 사용 가능한 메모리가 없는 경우 |
| NoClassDefFondException | 원하는 클래스를 찾지 못하였을 경우 |
| ArrayIndexOutOfBoundsException | 배열을 참조하는 인덱스가 잘못된 경우 |

위와 같은 예외는 개발자가 처리해줘도 되고 안해줘도 상관 없다. 

반면,  OtherException은 **무조건 예외 처리를 해줘야 한다.** 만약 처리하지 않으면 컴파일러가 컴파일 과정에서 컴파일 오류를 발생시킨다. 

예외 처리하는 방법 2가지

> 1. 예외를 잡아서 처리 :  try-catch-(finally), try-with-resources
> 
>      → 예외 발생 시 그 자리에서 처리해준다.
>      
>2. 메서드가 예외를 발생시킨다고 기술 : throws
>
>      → 자신이 처리하지 않고 throws를 통해 다른 메서드한테 예외 처리를 넘겨준다.
>
