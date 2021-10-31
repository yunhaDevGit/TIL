# CountDownLatch

 어떤 쓰레드가 다른 쓰레드에서 작업이 완료될 때까지 기다릴 수 있도록 해주는 클래스

 

예를 들어, Main thread에서 5개의 쓰레드를 생성하여 어떤 작업을 병렬로 처리되도록 할 수 있다.

이때, Main thread는 다른 쓰레드가 종료되는 것을 기다리지 않고 다음 코드를 수행한다.

여기서 CountDownLatch를 사용하면 다음 코드를 실행하지 않고 기다리도록 만들 수 있다.

 

### CountDownLatch 사용

CountDownLatch를 초기화 할 때 정수값 count를 넣는다. 쓰레드 마지막에서 countDown() 메서드를 호출하면 초기화할 때 넣어준 정수값이 하나 내려간다. 즉, 각 쓰레드는 마지막에서 자신이 실행 완료했음을 countDown() 메서드로 알려준다. 이 쓰레드들이 끝나기를 기다리는 쪽 입장에서는 await() 메서드를 불러준다. 그러면 현재 메서드가 실행 중인 메인 쓰레드는 더이상 진행하지 않고, CountDownLatch의 count가 0이 될 때까지 기다린다. 

 

**CountDownLatch 생성**

```
CountDownLatch countDownLatch = new CountDownLatch(5);
```

인자로 Latch의 숫자를 전달



**countDown()**

```
countDownLatch.countDown();
```

countDown() - latch의 숫자가 1개씩 감소



**await()** 

```
// countDownLatch의 count가 0이 될 때까지 기다립니다. 
countDownLatch.await();

// TimeOut 설정도 할 수 있습니다.
// 아래와 같이 설정할 경우, 만약 10초동안 countDownLatch의 count가 0이 되지 않는다면
// wait 상태를 해제하고 다음 동작으로 넘어갑니다.
countDownLatch.await(10, TimeUnit.SECONDS);
```

await() - latch의 숫자가 0이 될 때까지 기다리는 코드. 

다른 쓰레드에서 countDown()을 5번 호출하게 된다면 latch는 0이 되고, await()는 더 이상 기다리지 않고 다음 코드를 실행

일정 시간을 초과하면 작업을 기다리지 않도록, Timeout 설정도 할 수 있다
