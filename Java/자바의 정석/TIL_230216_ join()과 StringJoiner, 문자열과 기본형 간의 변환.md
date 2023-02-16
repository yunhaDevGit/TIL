# join()과 StringJoiner, 문자열과 기본형 간의 변환

- join()은 여러 문자열 사이에 구분자를 넣어서 결합한다.
- 많은 문자열 결합시 사용하면 좋다

```java
String animals = "dog,cat,bear";
String[] arr = animals.split(","); //문자열을 ','를 구분자로 나눠서 배열에 저장
String str = String.join("-", arr); //배열의 문자열을 '-'로 구분해서 결합
System.out.println(str); //dog-cat-bear
```

## 문자열과 기본형 간의 변환

- 숫자를 문자열로 바꾸는 방법

```java
int i=100;
String str1 = i+""; // 편리함.
String str2 = String.valueOf(i); // 속도가 빠름. 
```

- 문자열을 숫자로 바꾸는 방법

```java
int i = Integer.parseInt("100"); // 방법1
int i2 = Integer.valueOf("100"); // 방법2 
Integer i2 = Integer.valueOf("100"); // valueOf는 원래 반환 타입이 Integer
// (참조형을 기본형으로 사용해도 문제 없다:autoBoxing이 알아서 변환해줌)
```

