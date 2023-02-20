# StringBuffer 클래스

- String 처럼 문자형 배열(char[])을 내부적으로 가지고 있다

```java
public final class StringBuffer implements java.io.Serializable {
	private char[] value;
		...
}
```

- 그러나 String과 달리 내용을 변경할 수 있다(mutable)
    - 문자열이 많이 변경될 경우 사용하면 좋다

```java
StringBuffer sb = new StringBuffer("abc");
sb.append("123");
```

## StringBuffer의 생성자

- 배열은 길이 **변경 불가**. 공간이 부족하면 새로운 배열을 생성해야 한다.
- StringBuffer는 저장할 문자열의 길이를 고려해서 적절한 크기로 생성해야 한다

```java
public StringBuffer(int length) {
	value = new char[length];
	shared = false;
}

public StringBuffer() {
	this(16); // 버퍼의 크기를 지정하지 않으면 버퍼의 크기는 16이다
}

public StringBuffer(String str) {
	this(str.length() + 16); // 지정한 문자열의ㅏ 길이보다 16이 더 크게 버퍼 생성
	append(str);
}
```

## StringBuffer의 변경

- StringBuffer는 String과 달리 내용 변경이 가능하다
- append()는 지정된 내용을 StringBuffer에 추가 후, StringBuffer의 참조를 반환한다
- StringBuffer는 equals()가 오버라이딩 되어 있지 않다(주소 비교를 한다)
    - StringBuffer을 String을 변환 후 equals()로 비교해야 한다. (String은 equals를 오버라이딩 했다)
