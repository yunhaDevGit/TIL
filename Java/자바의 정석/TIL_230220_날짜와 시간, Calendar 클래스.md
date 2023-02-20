# 날짜와 시간, Calendar 클래스

## 날짜와 시간

- java.util.Date
    - 날짜와 시간을 다룰 목적으로 만들어진 클래스(JDK1.0)
    - Date의 메서드는 거의 deprecated 되었지만, 여전히 쓰이고 있다
- java.util.Calendar
    - Date 클래스를 개선한 새로운 클래스(JDK1.1)
    - 여전히 단점 존재

→ Date와 Calendar에서는 날짜와 시간을 항상 함께 다룬다는 단점 존재

- **java.time패키지**
    - Date와 Calendar의 단점을 개선한 새로운 클래스들을 제공(JDK1.8)
    - 날짜와 시간을 함께, 따로 다룰 수 있다
    - LocalDate, LocalTime, LocalDateTime
    

## Calendar 클래스

- 추상 클래스이므로 getInstance()를 통해 구현된 객체를 얻어야 한다.

```java
Calendar cal = new Calendar(); // error! 추상 클래스는 인스턴스를 생성할 수 없다

// getInstance() 메서드는 Calendar클래스를 구현한 클래스의 인스턴스를 반환한다
Calendar cal = Calendar.getInstance(); 
```

- get()으로 날짜와 시간 필드 가져오기 - int get(int field)

```java
Calendar cal = Calendar.getInstance(); //  현재 날짜와 시간으로 셋팅
int thisYear = cal.get(Calendar.YEAR); // 올해가 몇년인지 알아낸다
int lastDayOfMonth = cal.getActualMaximum(Calendar.DATE); // 이 달의 마지막 날
```

- Calendar에 정의된 필드
![image](https://user-images.githubusercontent.com/74949294/220119634-6d994d51-eeab-4e18-ac73-d58285df0b6f.png)

![image](https://user-images.githubusercontent.com/74949294/220119659-b1e9076a-0708-4108-8e68-2bb6b7f810b3.png)


    - Date 클래스를 개선한 새로운 클래스(JDK1.1)
