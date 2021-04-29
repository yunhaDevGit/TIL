# LinkedList

ArrayList와 LinkedList 모두 Java에서 제공하는 List 인터페이스를 구현한 Collection 구현체이다.

ArrayList는 배열을 사용해서 List를 구현한 클래스이다. ArrayList와 다르게 **LinkedList는 각각의 노드를 연결하는 방식을 사용**한다.



![img](https://blog.kakaocdn.net/dn/bWB4K6/btqOguNfqjR/LhBJEeOWo7zfwTpRsKm5Fk/img.png)



ArrayList는 한 덩어리의 큰 배열을 사용하는 방식이다. 

LinkedList는 양방향 연결 리스트로 구현되어 있다. 각각의 데이터가 노드(Node)로 구성되어 연결되는 구조이다,

각각의 노드는 데이터와 함께 next(다음 노드)와 prev(이전 노드) 값을 내부적으로 가지고 있다.

일반적으로 **LinkedList의 장점은 데이터를 추가하거나 삭제하는 것이 원활**하다는 것이다.

어느 위치에서든 **추가나 삭제를 할 경우 변경되는 노드만 다시 연결해주면 된다**

그래서 주로 **ArrayList는 검색이 많은 경우 사용**하고 **LinkedList는 잦은 삽입/삭제가 있는 경우 사용**한다



### LinkedList 생성

```java
import java.util.*;

LinkedList<Integer> integers1 = new LinkedList<Integer>();  // 타입 지정
LinkedList<Integer> integers2 = new LinkedList<>();  // 타입 생략 가능
LinkedList<Integer> integers3 = new LinkedList<>(integers1); // 다른 Collection 값으로 초기화
LinkedList<Integer> integers4 = new LinkedList<>(Arrays.asList(1,2,3,4)); //  Arrays.asList()
```

ArrayList와 다른 점은 가용량이 의미가 없기 때문에 가용량을 받는 생성자가 존재하지 않는다.





### LinkedList element 추가/변경/삭제

ArrayList와 동일하게 List를 구현했으므로 add() 메서드로 추가할 수 있다.

```java
LinkedList<String> colors = new LinkedList<>();

colors.add("black"); // [black]
colors.add("white"); // [black, white]
colors.add(0,"green"); // [green, black, white]

colors.set(0,"blue"); // [blue, black, white]

colors.remove(0); // [black, white]
colors.remove("white"); // [black]
colors.clear();
```



### LinkedList 순환(ArrayList와 방법 동일)

```java
import java.util.*;

public class LinkedListTest {
    public static void main(String[] args) {
        LinkedList<String> colors = new LinkedList<>(Arrays.asList("black","white","green","red"));
        
        // for loop
        int colorsSize = colors.size(); 
        // 만약 for문 안에 colors.size()로 했다면 코드를 반복할 때마다 colors.size()를 호출한다.
        for(int i=0;i<colorsSize;i++){
            System.out.print(colors.get(i) + " ");
        }
        
        // for-each
        for(String color : colors) {
            System.out.print(color + " ");
        }
        
        // iterator
        Iterator<String> iterator = colors.iterator(); // list의 모든 값을 가져온다
        while(iterator.hasNext()){
            System.out.print(iterator.next() + " ");
        }
        
        // listIterator
        ListIterator<String> listIterator = colors.listIterator(colors.size());
        while(listIterator.hasPrevious()) {
            System.out.print(listIterator.previous()+" ");
        }
    }
}
```



### LinkedList 값 존재 유무 확인(ArrayList와 동일)

```java
LinkedList<String> colors = new LinkedList<>(Arrays.asList("black","white","red"));

boolean conatins = colors.contains("black"); // true

int index = colors.indexOf("white"); // 1

index = colors.indexof("blue"); // -1
```

