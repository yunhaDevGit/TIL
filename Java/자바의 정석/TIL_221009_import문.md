# import문

- 클래스를 사용할 때 패키지 이름을 생략할 수 있다.

```java
// ---- import 사용 X
class ImportTest {
	java.util.Date today = new java.util.Date();
}

// ---- import 사용
import java.util.Date;

class ImportTest {
	Date today = new Date();
}
```

- java.lang 패키지(자바의 기본 패키지)의 클래스는 import 하지 않고 사용할 수 있다.
    - String, Object, System, Thread 등…

```java
import java.lang.*

class ImportTest2 {
	public static void main(String[] args) {
		 System.out.println("Hello World");
	}
}		
```

## import 문의 선언

- import 문을 선언하는 방법은 다음과 같다
    - import 패키지명.클래스명;
    - import 패키지명.*; // 모든 클래스
- import 문은 패키지문과 클래스 선언의 사이에 선언한다
- import문은 컴파일 시에 처리 되므로 프로그램의 성능에 영향 없다
위와 아래 성능이 같다ㅎㅎ
    
    ```java
    import java.util.Calendar;
    import java.util.Date;
    import java.util.ArrayList;
    
    import java.util.*;
    ```
    
- 이름이 같은 클래스가 속한 두 패키지를 import할 때에는 클래스 명 앞에 패키지 경로를 써주면 된다.

## static import문

- static멤버를 사용할 때 클래스 이름을 생략할 수 있게 해준다
    - 필요할 때만 쓸 것
  ```java
  
 import static java.lang.Integer.*; // Integer 클래스의 모든 static 메서드
import static java.lang.Math.random; // Math.random()만, 괄호 안붙임
import static java.lang.System.out; // System.out을 out만으로 참조 가능

class Ex7_6 {
	public static void main(String[] args) {
		// System.out.pringln(random());
		out.pringln(random());

		// System.out.pringln(random());
		out.println("Math.PI:" + PI);
	}
}
```
