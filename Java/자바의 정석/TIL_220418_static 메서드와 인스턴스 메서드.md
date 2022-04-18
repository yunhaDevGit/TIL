# static 메서드와 인스턴스 메서드

### static 메서드

- 메서드 앞에 static이 붙은 것
- 클래스 메서드
- 언제든지 사용할 수 있다
- 객체 생성 없이 ‘클래스이름.메서드이름()’으로 호출
    - 인스턴스 멤버가 필요 없다
- 인스턴스 멤버와 관련 없는 작업을 하는 메서드
- 메서드 내에서 인스턴스 변수 사용 불가

### 인스턴스 메서드

- 메서드 앞에 static이 붙지 않은 것
- 인스턴스를 생성한 후에 사용할 수 있다
- 인스턴스 생성 후 ‘참조변수.메서드이름()’으로 호출
- 인스턴스 멤버와 관련된 작업을 하는 메서드
- 메서드 내에서 인스턴스 변수 사용 가능

> **static 메서드와 인스턴스 메서드의 차이**
- 인스턴스 변수를 사용하냐 안하냐
> 

```java
class MyMath {
	long a,b;

	long add() { // 인스턴스 메서드
		return a + b;
	}
	
	static long add(long a, long b) { // 클래스 메서드
		return a + b;
	}
}

// 클래스 메서드 호출 방법
System.out.println(MyMath.add(200L, 100L)));

// 인스턴스 메서드 호출 방법
MaMath mm = new MyMath();
System.out.println(mm.add()));
```

> **언제 static 메서드를 사용하나??**

→ 인스턴스 변수를 사용하지 않을 때 사용한다
→ 위의 예제를 보면 static 클래스 변수는 지역변수를 매개변수로 받기 때문에 인스턴스 변수가 필요 없다. 
→ 반면 인스턴스 변수는 입력값이 없다(매개변수가 없다) 때문에 인스턴스 변수가 필요하다.
> 

**static을 언제 붙어야 할까?**

- 속성(멤버 변수) 중에서 공통 속성에 붙인다.
- 인스턴스 멤버를 사용하지 않는 메서드에 static을 붙인다.

```java
class Test{
	int iv;  // 인스턴스 변수
	static int cv;  // 클래스 변수

	void instanceMethod() {  // 인스턴스 메서드
		System.out.printnln(iv); // 인스턴스 변수를 사용할 수 있다
		System.out.printnln(cv); // 클래스 변수를 사용할 수 있다
	}

	static void staticMethod() { // static 메서드
		System.out.printnln(iv);  // 에러!!인스턴스 변수를 사용할 수 없다(클래스 메서드는 객체 생성 없이도 항상 사용할 수 있기 때문)
		System.out.printnln(cv);  // 클래스 변수는 사용할 수 있다
	}

	void instanceMethod2() {  // 인스턴스 메서드
		instanceMethod(); // 인스턴스 메서드를 호출한다
		staticMethod(); // 클래스 메서드를 호출한다
	}

	static void staticMethod2() {  // 인스턴스 메서드
		instanceMethod(); //에러!인스턴스 메서드를 호출할 수 없다.객체를 생성해야 호출할 수 있다.
		staticMethod(); // 클래스 메서드를 사용할 수 있다
	}

}
```

*static 메서드는 인스턴스 변수와 인스턴스 메서드를 호출할 수 없다*
