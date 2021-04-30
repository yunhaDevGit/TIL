# Queue

Queue는 FIFO(First In First Out) 형태를 가진다. 즉, 먼저 들어온 데이터가 먼저 나가는 구조이다.

<img src="https://blog.kakaocdn.net/dn/bhvAPe/btqHlVqf0RY/Y4oCoA4wUkEpvIkU80i43K/img.png" alt="img" style="zoom: 50%;" />

Enqueue - 큐 맨 뒤에 데이터 추가

Dequeue - 큐 맨 앞쪽 데이터 삭제



### Queue 특징

- 먼저 들어간 자료가 먼저 나오는 FIFO 구조
- 큐는 한 쪽 끝은 front로 정하여 삭제 연산만 수행한다
- 다른 한 쪽 끝은 rear로 정하여 삽입 연산만 수행한다
- 너비 우선 탐색(BFS)에 사용



### Queue 사용

##### Queue 선언

```java
import java.util.LinkedList;
import java.util.Queue;

Queue<Integer> queue = new LinkedList<>();
Queue<String> queue = new LinkedList<>();
```

자바에서 Queue는 LinkedList를 활용하여 생성해야 한다.



##### Queue 값 추가

```java
Queue<Integer> queue = new LinkedList<>();
queue.add(1); // 성공시 true,저장공간 부족하면 IllegalStateException 발생
queue.add(2); 
queue.offer(3); // 성공시 true, 실패시 false
```

add 또는 offer로 queue에 값을 추가할 수 있다.



##### Queue 값 읽기

```java
Queue<Integer> queue = new LinkedList<>();
queue.offer(1);
queue.offer(2);
queue.element(); // queue 값을 삭제없이 읽어오기, queue 비어있을 경우 NoSuchElementException 발생
queue.peek();    // queue 값을 삭제없이 읽어오기, queue 비어있을 경우 null 반환
```



##### Queue 값 삭제

```java
Queue<Integer> queue = new LinkedList<>();
queue.offer(1);
queue.offer(2);
queue.offer(3);
queue.poll();   // queue에 첫번째 값 반환 후 제거. 비어있다면 null 반환
queue.remove(); // queue에 첫번째 값 제거, 비어있다면 NoSuchElementException 발생
queue.clear();  // queue 초기화
```



참조 : https://pridiot.tistory.com/68

​		  https://coding-factory.tistory.com/602