## switch문

처리해야 하는 경우의 수가 많을 때 유용한 조건문이다.

```java
switch(조건식) {
  case 값1:
    // 조건식의 결과가 값1과 같은 경우 수행될 문장
    // ...
    break;
  case 값2:
    // 조건식의 결과가 값2와 같은 경우 수행될 문장
    // ...
    break;
  default:
    // 조건식의 결과와 일치하는 case문이 없을 때 수행될 문장들
}
```

**동작 과정**

1. 조건식을 계산한다.
2. 조건식의 결과와 일치하는 case문으로 이동한다.
3. 이후의 문장들을 수행한다.
4. break문이나 switch문의 끝을 만나면 switch문 전체를 나간다.



### if-else문보다 switch문이 유용한 경우?

- 처리해야 하는 경우의 수가 많을 때 유용하다.
- if-else문의 조건식은 결과과 true/false여야 하지만 switch문은 다양한 값이 나올 수 있다.
- If-else문은 조건식이 여러 개가 나와야 하지만 switch문은 조건식이 하나면 된다.



### switch문의 제약 조건

1. switch문의 조건식의 결과는 **정수** 또는 **문자열**(JDK 1.7부터 허용)이어야 한다.
2. case문의 값은 정수 상수(문자 포함), 문자열만 가능하며, 중복되지 않아야 한다.
   - 변수는 안된다.

example

```java
int num = 2;

...
  
switch(result) {
  case '1':    // ok, 문자 리터럴
  case ONE:    // ok, 정수 상수
  case "YES":  // ok, 문자열 리터럴. JDK 1.7부터 사용 가능
  case num:    // error, 변수는 불가
  case 1.0:    // error, 실수는 불가
}
```



