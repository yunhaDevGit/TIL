# Java Exception

## Error

- java 동작 환경에서의 문제가 발생 한 것
    - 직접 만든 코드에서의 에러가 아님
- 운영체제에 문제, 메모리 부족 등

## Exception

- 프로그램에서 예상하지 못했던 예외적인 상황
- 잘못 된 값을 입력, 잘못 된 연산 등
    
    ```java
    public class ExceptionApp {
    	public static void main(String[] args) {
    		System.out.println(1);
    		System.out.println(2/0); // ArithmeticException
    		System.out.println(3);
    	}
    }
    
    // 결과
    1
    ArithmeticException ...
    ```
    

## 예외 처리

```java
public class ExceptionApp {
	public static void main(String[] args) {
		System.out.println(1);
		try {
			System.out.println(2/0); // Exception
		} catch (ArithmeticExceptoin e) {
			System.out.println("잘못된 계산입니다");
		}
		System.out.println(3);
	}
}

// 결과
1
잘못된 계산입니다
3
```
