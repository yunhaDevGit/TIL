#### Spring Boot Scheduler 설정



주기적인 작업이 있을 때 @Scheduled 어노테이션을 사용하여 쉽게 적용할 수 있다. 

다양한 속성들이 있는데, cron과 fixedRate를 보자.

fixedRate : milliseconds 단위로 이전 작업이 수행되기 시작한 시점으로부터 고정된 시간을 설정

​					ex) **@ScheduledfixedRate = "3000")**

cron : cron 표현식을 지원. "초 분 시 일 월 주 (년)"으로 표현.

​					ex) **@Scheduled(cron="* * * * *")**

![img](https://lh6.googleusercontent.com/VW_RRbJyzE_gVN7hDV_ecTdo_cfpXkLN5iX6IDMbq-voA2ogVoclQYMHtC3Pxjd9tDHxwAhb6vD4O8VptyHtcP3mYn6pRERcWKwWSP2e2H7qx0RWcpuhuxfUXGD6GVW3XrNkOGmZ)



```
cron 속성

초 0-59 , - * / 
분 0-59 , - * / 
시 0-23 , - * / 
일 1-31 , - * ? / L W
월 1-12 or JAN-DEC , - * / 
요일 1-7 or SUN-SAT , - * ? / L # 
년(옵션) 1970-2099 , - * /

* : 모든 값
? : 특정 값 없음
- : 범위 지정에 사용
, : 여러 값 지정 구분에 사용
/ : 초기값과 증가치 설정에 사용
L : 지정할 수 있는 범위의 마지막 값
W : 월~금요일 또는 가장 가까운 월/금요일
# : 몇 번째 무슨 요일 2#1 => 첫 번째 월요일
```

https://huskdoll.tistory.com/819



#### SimpleDateFormat

- 날짜를 원하는 포맷으로 파싱시켜주는 역할

  https://jamesdreaming.tistory.com/99

![img](https://lh5.googleusercontent.com/IrQRd8pWFo3oR9D0YjggD3nBRRXBrBZhz-FzCY8wrbuZ-T4ygSCn7UImQRPseCzLTSuh9x_QMuJkfZo0UETApV79h2lXnG0Kn18OvARxBm5pShnUatHy9LWC5nOhx2RN8ENI_wu5)



#### Calendar.getInstance()

- 현재 날짜와 시간 정보를 가진 Calendar 객체 생성

  https://hyeonstorage.tistory.com/205