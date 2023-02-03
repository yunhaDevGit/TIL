# printStackTrace()와 getMessage()

```
printStackTrace() : 예외 발생 당시의 **호출스택에** 있었던 메서드의 정보와 예외 메세지를 **화면에 출력**
getMessage() : 발생한 예외클래스의 인스턴스에 저장된 메세지를 얻을 수 있다
```

### 멀티 catch 블럭

- 내용이 같은 catch 블럭을 하나로 합친 것

```java
try {
	// ...
} catch(Exception1 e) {
	e.printStackTrace();
} catch(Exception2 e2) {
	e2.printStackTrace();
}

// 멀티 catch 블럭
try {
	// ...
} catch(Exception1 | Exception2 e) {
	e.printStackTrace();
}
```
