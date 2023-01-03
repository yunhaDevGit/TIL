# Java Timer

## Timer 클래스와 TimerTask 클래스

********************************Timer 클래스********************************는 실제 타이머의 기능을 수행하는 클래스이고, **********************************************TimerTask 클래스**********************************************는 “Timer” 클래스가 수행되어야 할 내용을 작성하는 클래스입니다.  TimerTask 클래스는 **************************************************run 메소드 재정의**************************************************를 합니다.

### java.util.Timer 클래스

Timer 클래스는 아래와 같이 3가지 메소드를 제공합니다. 

- schedule
- scheduleAtFixedRate
- cancel

이 중, schedule 메소드는 4가지 형태로 오버로딩 되어 있다.

```java
void schedule(TimerTask task, Date time) // 설정한 time 시간에, 설정한 task 작업을 수행
void schedule(TimerTask task, Date firstTime, long period) // firstTime부터 period 간격으로 task 작업 수행
void schedule(TimerTask task, long delay) // delay 시간이 지난 후에 period 간격으로 task 수행
void schedule(TimerTask task, long delay, long period) // delay 시간이 지난 후에, period 간격으로 task 수행
```

위의 두 메소드의 경우 만약 지정한 시간이 현재 시간보다 이전일 경우 바로 task를 수행합니다. 

나머지 두 개의 메소드는 f**ixed-delay 방식**으로 작업을 진행하게 되는데, fixed-delay 방식은 **선행 작업이 지연될 경우, 다음에 수행되는 작업 역시 그 시간만큼 지연되는 방식**입니다. 

scheduleAtFixedRate 메소드는 fixed-delay 방식이 아니므로, 정확하게 일정 시간 간격으로 작업을 실행해야 할 때 적합합니다. 

즉, **scheduleAtFixedRate()** 메소드는 **fixed-rate 방식**을 사용하는데, **선행 작업의 지연 유무에 상관없이 지정된 시간에 작업을 실행**합니다.

### java.util.TimerTask 클래스

TimerTask 클래스는 Timer 클래스가 수행할 작업을 나타냅니다. 

TimerTask는 Runnable 인터페이스를 구현하고 있습니다. 

```java
boolean cancel() // TimerTask 작업을 취소
abstract void run() // TimerTask가 실행할 작업
long scheduledExecutionTime() // 가장 최근에 작업이 실행된 시간 리턴
```

```java
import java.util.Timer;
import java.util.TimerTask;

public class Main {
	public static void main(String[] args) {
		Timer timer = new Timer();
		TimerTask timerTask = new TimerTask() {
			int cnt = 0;
			@Override
			public void run() {
				if(cnt++ <5) {
					System.out.println("task...");
				} else {
					timer.cancle();
				}
			}
		};
		timer.schedule(timerTask, 1000, 1000);
	}
}
```

위의 코드는 프로그램이 1초 뒤 1초마다 5번만큼 task…을 출력하는 코드입니다. 

다만, Timer 클래스의 기본 생성자를 사용하여 Timer 클래스를 생성하면, 데몬 스레드로 실행되지 않습니다. 

데몬 스레드로 지정하고 싶은 경우 생성자를 생성할 때, boolean 인자를 받는 생성자로 입력 받으면 됩니다.

[https://sangwoo0727.github.io/java/JAVA-36_timertask/](https://sangwoo0727.github.io/java/JAVA-36_timertask/)
