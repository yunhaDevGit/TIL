# SimpleDateFormat

- 날짜와 시간을 다양한 형식으로 출력할 수 있게 해준다

```java
Date today = new Date();
SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd");

String result = df.format(today);
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8bb807d1-6bb3-4efb-ac90-e6affccfb121/Untitled.png)

- 특정 형식으로 되어 있는 문자열에서 날짜와 시간을 뽑아낼 수 있다

```java
DateFormat df = new SimpleDateFormat("yyyy년 MM월 dd일");
DateFormat df2 = new SimpleDateFormat("yyyy/MM/dd");
Date d = df.parse("2023년 2월 20일"); // 문자열 Date로 변환
String result = df2.format(d);
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/97036f3f-55f3-4c21-9187-3c708bf9b1a4/Untitled.png)