# 참조변수 super, 생성자 super()

## 참조변수 super

- 객체 자신을 가리키는 참조 변수.
- 인스턴스 메서드(생성자) 내에만 존재
- 조상의 멤버를 자신의 멤버와 구별할 때 사용한다
    - this는 lv와 iv 구별할 때 사용한다.

### 조상 멤버와 객체 자신의 멤버의 이름이 겹칠 경우 상속 된다.

- 둘 다 존재하지만 조상 멤버는 super로 자신의 멤버는 this로 호출한다.

```java
class Ex7_2 {
    public static void main(String args[]) {
        Child c = new Child();
        c.method();
    }
}

class Parent {
    int x = 10; // super.x
}

class Child extends Parent {
		int x =20; // this.x

    void method() {
        System.out.println("x=" + x);
        System.out.println("this.x=" + this.x);
        System.out.println("super.x=" + super.x);
    }
}
```

### 객체 자신의 멤버가 없을 경우 조상으로부터 상속 받는다.

- 멤버가 없더라도 조상으로부터 상속 받은 것이 있기 때문에 super.x와 this.x 모두 가능하다.
- 조상 멤버이면서 객체 자신의 멤버이다.

```java
public class Ex7_3 {
    public static void main(String[] args) {
        Child2 c = new Child2();
        c.method();
    }
}

class Parent2 {
    int x = 10; // super.x와 this.x 둘 다 가능
}

class Child2 extends Parent2 {
    void method() {
        System.out.println("x=" + x);
        System.out.println("this.x=" + this.x);
        System.out.println("super.x=" + super.x);
    }
}
```

## super() - 조상의 생성자

*참조변수 super와는 전혀 관계 없다.*

- 조상의 생성자를 호출할 때 사용 (`this()`)
    - 상속 시 **생성자, 초기화 블록**은 상속되지 않는다.
- 조상의 멤버는 조상의 생성자를 호출해서 초기화

```java
class Point {
	int x, y;

	Point(int x, int y) {
		this.x = x;
		this.y = y;
	}
}
```

Point3D 예제의 경우 자손의 생성자에서 부모의 멤버까지 초기화 한다. 

해당 예제는 잘못된 예제!! 자손의 생성자는 자신이 선언한 멤버만 초기화 해야 한다.

```java
class Point3D extends Point {
	int z;

	Point3D(int x, int y, int z) {
		this.x = x; // 조상의 멤버를 초기화 (X) 자손의 생성자는 자신이 선언한 것만 초기화 해야 한다.
		this.y = y; // 조상의 멤버를 초기화 (X)
		this.z = z;
	}
}

------ correct example
Point3D(int x, int y, int z) {
	super(x, y); // 조상 클래스의 생성자 Point(int x,int y)를 호출
	this.z = z; // 자신의 멤버를 초기화
}

```

### 추가 조건!

- **생성자의 첫 줄에 반드시 생성자를 호출해야 한다** ‼️
    - 그렇지 않으면 컴파일러가 생성자의 첫 줄에 `super();`를 삽입한다.

컴파일 전

```java
class Point {
	int x;
	int y;

	Point() {
		this(0, 0);
	}

	Point(int x, int y) {
		this.x = x;
		this.y = y;
	}
}
```

컴파일 후

```java
class Point {
	int x;
	int y;

	Point() {
		this(0, 0);
	}

	Point(int x, int y) {
		super(); // Object();
		this.x = x;
		this.y = y;
	}
}
```

**예제!! 중요!!!**

```java
package chapter7;

public class Point {
    int x;
    int y;

    Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    String getLocation() {
        return "x :" + x + "y : " + y;
    }
}

class Point3D extends Point {
    int z;

    Point3D(int x, int y, int z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    String getLocation() {
        return "x :" + x + "y : " + y + "z : " + z;
    }
}

class PointTest {
    public static void main(String[] args) {
        Point3D p3 = new Point3D(1,2,3);
    }
}
```

위의 코드 실행 시 컴파일 에러 발생

```java
java: constructor Point in class chapter7.Point cannot be applied to given types;
  required: int,int
  found: no arguments
  reason: actual and formal argument lists differ in length
```

![image](https://user-images.githubusercontent.com/74949294/194341304-f6cf77fe-31e4-4cd7-ae3e-50102b509629.png)

**원인**

모든 생성자는 첫 줄에 다른 생성자를 호출해야 한다.

만약 다른 생성자가 없을 경우 컴파일러가 자동으로 `super()`를 넣어준다. 즉, Point(..)와 Point3D(..) 생성자 모두 `super()` 가 들어간다. 

Point(..) 생성자에서는 `super()`를 통해 `Object()` 생성자를 호출하고, Point3D(..) 생성자에서는 `super()`를 통해 `Point()` 생성자를 호출한다.

그러나 이때, Point() 기본 생성자가 없기 때문에 에러가 발생한다.

![image](https://user-images.githubusercontent.com/74949294/194341344-97320501-ea68-4086-b9e2-67a828c77b31.png)
