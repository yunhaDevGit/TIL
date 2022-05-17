# 생성자 this(), 참조변수 this

### 생성자 this()

- 생성자에서 다른 생성자를 호출할 때 사용
- 다른 생성자 호출 시 첫 줄에서만 사용 가능
    - 코드의 중복을 제거하기 위해 생성자들끼리 호출하곤 한다

```java
class Car2 {
	String color;  // iv
	String gearType;
	int door;

	Car2() {
		this("white", "auto", 4);
	}

	Car2(String color) {
		this(color, "auto", 4);
	}

	Car2(String color, String gearType, int door) {
		this.color = color;
		this.gearType = gearType;
		this.door = door;
	}
}
```

**코드의 중복을 제거한 코드**

```java
// 변경 전
Car() {
	color = "white";
	gearType = "auto";
	door = 4;
}

Car(String color, String gearType, int door) {
	color = color;
	gearType = gearType;
	door = door;
}

// 변경 후
Car2(String color) {
	this("White", "auto", 4);
}
```

### 참조변수 this

- 인스턴스 자신을 가리키는 참조변수
    - 인스턴스의 주소가 저장되어 있다
- 모든 인스턴스 메서드에 지역변수로 숨겨진 채로 존재한다
- 인스턴스 메서드(생성자 포함)에서 사용 가능
- 지역 변수(lv)와 인스턴스 변수(iv)를 구별할 때 사용

```java
Car(String c, String g, int d) {
	// color는 lv, c는 iv
	color = c;
	gearType = g;
	door = d;
}
```

```java
Car(String color, String gearType, int door) {
	// this.color는 iv, color는 lv
	this.color = color;
	this.gearType = gearType;
	this.door = door;
}
```

> this와 this()는 비슷하게 생겼을 뿐 완전히 다르다.
this는 `참조 변수`이고, this()는 `생성자`이다
>
