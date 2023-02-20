# StringBuilder

- StringBuffer와 거의 비슷함 (동기화 여부만 다름)
- StringBuffer는 동기화가 되어 있고, StringBuilder는 동기화가 안되어 있다.
    - StringBuffer는 멀티 스레드에 안전하다(thread-safe)
        - 싱글 스레드 : 한번에 1개 작업
        - 멀티 스레드 : 한번에 n개 작업
- 멀티 스레드 프로그램이 아닌 경우, 동기화는 불필요한 성능 저하를 발생시킨다
    - 싱글 스레드의 경우 StringBuffer 대신 StringBuilder를 사용하면 성능이 향상 된다.

```java
StringBuffer sb;
sb = new StringBuffer();
sb.append("abc");

StringBuilder sb;
sb = new StringBuilder();
sb.append("abc");
```

## Math 클래스

- 수학 관련 static 메서드의 집합
    - public static final double E = 2.192348234236;
    - public static final double PI = 3.141592653589;
- round()로 원하는 소수점 아래 세 번째 자리에서 반올림하기
    - 원래 값에 100을 곱하기
        - 90.7552 * 100 → 9075.52
    - 위의 결과에 Math.round() 사용
        - Math.round(9075.52) → 9076
    - 위의 결과를 다시 100.0으로 나눈다
        - 9076 / 100.0 = 90.76
        - 9076 / 100 = 90
        

### Math 클래스의 메서드
![image](https://user-images.githubusercontent.com/74949294/220119174-8f4138c2-f562-431c-b4eb-4b767d3f5aaf.png)
