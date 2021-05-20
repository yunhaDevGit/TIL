
# LocalDate, LocalTime, LocalDateTime

Java 8부터 java.time API를 사용할 수 있게 되었다. 
기존에 쓰던 Calendar와 Date 클래스들의 아래와 같은 단점으로 인해 이제는 java.time API를 사용하는 것이 좋다.
java.time은 Java 8 이상부터 사용할 수 있으니 참고하자!

## Calendar, Date 클래스의 문제점
**1. 상수필드 남용**

     calendar.add(Calendar.SECOND,2)

**2. 헷갈리는 월 지정**
   
     1월=0 2월=1 ... 

**3. 일관성이 없는 요일 상수**

    Date.getDay()에서는 일요일이 0
    Calendar.get(Calendar.DAY_OF_WEEK) 일요일이 1
  
**4. Date와 Calendar 객체의 역할분담**

   년/월/일 계산은 Date 클래스만으로는 부족해서 왔다갔다 하는 문제가 있다.

   (특정 시간대의 날짜를 생성하거나, 년/월/일 같은 날짜 단위 계산을 Date 클래스만으로는 부족하여 연산을 위해 Calendar 객체를 생성 후, 다시 Calendar객체에서 Date 객체를 생성해야 한다)

   또한 Calendar 객체를 생성하고 Date 객체를 생성하는 프로세스를 거치기 때문에 번거롭고 생성비용이 싸다.

**5. 기타 java.util.Date 하위 클래스 문제**

   - java.sql.Date 클래스의 상위 클래스인 java.util.Date와 클래스 이름이 같다

   - java.sql.Date 클래스의 Comporable 인터페이스에 대한 정의를 클래스 선언에서 하지 않아 Comporable 관련 Generics 선언이 복잡하다

   - java.sql.TimeStamp 클래스는 java.util.Date 클래스에 나노초 필드가 더해진 클래스이다. 이 클래스는 equals() 선언의 대칭성을 어겼다. 때문에 Date 타입과 TimeStamp 타입을 섞어 쓰면 'a.equals(b) = true'여도 'b.equals(a) = false'인 경우가 생길 수 있다


### java.time 패키지
아래 세개의 클래스는 불변 객체이다.
- LocalDate : 년,월,일을 표현하는 클래스 (ex, 2021-05-20)
- LocalTime : 시,분,초를  표현하는 클래스
- LocalDateTime : 년,월,일,시,분,초를 표현하는 클래스 (ex, 2021-05-20T10:27:37)


### LocalDate

    Localdate current = LocalDate.now(); // 2021-05-20
    LocalDate target = LocalDate.of(int year, int month, int dayofMonth);

| 리턴 타입 | 메소드(매개변수) |  설명 |
|--|--|--|
| int | getYear() | 년 |
| Month | getMonth() | Month 열거 값(JANIARY, FEBURARY..) |
| int | getMonthValue() | 월(1,2,3..) |
| int | getDayOfYear() | 1년의 몇 번째 일 |
| int | getDayOfMonth() | 월의 몇 번째 일 |
| DayOfWeek | getDayOfWeek() | 요일(MONDAT,TUESDAY...) |
| boolean | isLeapYear() | 윤년 여부 |


### LocalTime

    LocalTime currentTime = LocalTime.now(); // 12:07:56.453
    LocalTime targetTime = LocalTime.of(int hour, int minute, int second, int nanoOfSecond);

| 리턴 타입 | 메소드(매개변수) |  설명 |
|--|--|--|
| int | getHour() | 시간 |
| int | getMinute() | 분 |
| int | getSecond() | 초 |
| int | getNano() | 나노초 |



### LocalDateTime

    LocalDateTime currentTime = LocalDateTime.now(); // 2021-05-20T12:07:56.453
    LocalDateTime targetTime = LocalDateTime.of(int year, int month, int dayofMonth, int hour, int minute, int second, int nanoOfSecond);



## 날짜, 시간 연산 메소드

**LocalDate, LocalDateTime의 년,월,일,주 연산**

    LocalDate currentDate = LocalDate.now();
    LocalDate targetDate = currentDate
		    .plusYears(long)
		    .minusYears(long)
		    .plusMonths(long)
		    .minusMonths(long)
		    .plusDays(long)
		    .minusDays(long)
		    .plusWeeks(long)
		    .minusWeeks(long);


**LocalTime, LocalDateTime의 시,분,초,나노초 연산**

    LocalTime currentTime = LocalTime.now();
    LocalTime targetTime = currentTime
		    .plusHours(long)
		    .minusHours(long)
		    .plusMinutes(long)
		    .minusMinutes(long)
		    .plusSeconds(long)
		    .minusSeconds(long)
		    .plusNanos(long)
		    .minusNanos(long);

**LocalDate, LocalDateTime의 년,월,월의 일, 년도의 일 변경**

    LocalDate targetDate = currentDate
		    .withYear(int) // 년도 변경
		    .withMonth(int) // 월 변경
		    .withDayOfMonth(int) // 월의 일 변경
		    .withDayOfYear(int) // 년도의 일 변경
		    .with(TemporalAdjuster adjuster) // 현재 날짜를 기준으로 상대적인 날짜로 변경

TemporalAdjuster 예제

    LocalDate targetDate = currentDate
		    .withYear(TemporalAdjuster.firstDayOfYear()) // 이번 년도의 첫번째 일(1월 1일)
		    .withYear(TemporalAdjuster.lastDayOfYear()) // 이번 년도의 마지말 일(12월 31일)
		    .with(TemporalAdjuster.firstDayOfMonth()) // 이번 달의 첫 번째 일(1일)
		    .with(TemporalAdjuster.firstInMonth(DayOfWeek.MONDAY)) // 이번 달의 첫번째 요일(여기서는 월요일)
		    ...;
		  

**LocalTime, LocalDateTime의 시,분,초,나노초 변경**

    LocalTime targetTime = currentTime
		    .withHour(int)
		    .withMinute(int)
		    .withSecond(int)
		    .withNano(int);



## 날짜 시간 비교 메소드
### 날짜 비교
리턴 타입 : boolean
- isBefore() : startDateTime이 endDateTime보다 이전 날짜인지 비교
- isEqual() : 동일 날짜인지 비교
- isAfter() :  startDateTime이 endDateTime보다 이후 날짜인지 비교

        LocalDateTime startDateTime = LocalDateTime.now();
	    LocalDateTime endDateTime = LocalDateTime.of(2021, 6, 1, 14, 59, 59);
    
	    startDateTime.isBefore(endDateTime); // true
	    startDateTime.isEqual(endDateTime); // false
	    startDateTime.isAfter(endDateTime); // false


### 시간 비교
리턴 타입 : boolean
- isBefore() : startTime이 endTime보다 이전 시간인지 비교
- isAfter() :  startTime이 endTime보다 이후 시간인지 비교

        LocalTime startTime = LocalTime.now();
	    LocalTime endTime = LocalTime.of(14, 59, 59);
    
	    startTime.isBefore(endTime); // true
	    startTime.isAfter(endTime); // false



### 날짜 차이 계산
리턴 타입 : Period
- until(endDate), between(startDate, endDate) : 날짜 차이 계산
- getYears()
- getMonths()
- getDays()

        LocalTime currentDate = LocalTime.now();
	    LocalTime targetDate = LocalTime.of(2021, 6, 1);
    
	    Period period = currentDate.until(targetDate);
	    
	    period.getYears(); // 0년
	    period.getMonths(); // 1개월
	    period.getDays(); // 12일
	    
	    Period period = Period.between(currentDate, targetDate);
	    
	    period.getYears(); // 0년
	    period.getMonths(); // 1개월
	    period.getDays(); // 12일


### 시간 차이 계산
리턴 타입 : long, Duration
- until(end, unit) : 시간 차이 계산 (return : boolean)
- between(start, end) : 시간 차이 계산 (return : Duration)

        LocalTime startTime = LocalTime.now();
	    LocalTime endTime = LocalTime.of(12, 55, 40);
    
	    statTime.util(endTime, ChronoUnit.HOURS);
    
	    Duration duration = Duration.between(startTime, endTime);
	    duration.getSeconds();
	    duration.getNano();




## 날짜 포맷팅
- format(DateTimeFormatter formatter) 를 사용해서 원하는 문자열로 변환

	    LocalDateTime now = LocalDateTime.now();
	    DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("YYYY년 M월 d일 a h시 m분");
	    String nowString = now.format(dateTimeFormatter); // 2021년 5월 20일 오후 5시 10분


### Epoch Time을 LocalDate로 변환

	    Long epochTime = 1621486881;
	    LocalDate localdate = Instant.ofEpochMilli(Long.parseLong(epochTime))
			    .atZone(ZoneId.systemDefault()).toLocalDate();
		

참조 

https://d2.naver.com/helloworld/645609

https://jeong-pro.tistory.com/163

http://blog.eomdev.com/java/2016/04/01/%EC%9E%90%EB%B0%948%EC%9D%98-java.time-%ED%8C%A8%ED%82%A4%EC%A7%80.html

