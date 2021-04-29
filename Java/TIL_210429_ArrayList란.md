## ArrayList란?

자바의 List 인터페이스를 상속받은 여러 클래스 중 하나이다.

ArrayList는 일반 배열과 동일하게 연속된 메모리 공간을 사용하며 인덱스는 0부터 시작한다.

**배열과의 차이점**은 배열은 크기가 고정인 반면 **ArrayList는 크기가** **가변적으로 변한다**

내부적으로 저장이 가능한 메모리 용량(Capacity)이 있으며 현재 사용 중인 공간의 크기(Size)가 있다. 

만약 현재 **가용량(Capacity) 이상을 저장하려고 할 때 더 큰 공간의 메모리를 새롭게 할당**한다.



*객체들이 추가되어 저장 용량(Capacity)를 초과한다면 자동으로 부족한 크기만큼 늘어난다는 특징을 갖는다*



#### ArrayList 생성

```java
import java.util.ArrayList;

...

ArrayList<Integer> integers1 = new ArrayList<Integer>(); // 타입 지정
ArrayList<Integer> integers2 = new ArrayList<>(); // 타입 생략 가능
ArrayList<Integer> integers3 = new ArrayList<>(10); // 초기 용량(Capacity) 설정
ArrayList<Integer> integers4 = new ArrayList<>(integers1); // 다른 Collection 값으로 초기화
ArrayList<Integer> integers5 = new ArrayList<>(Arrays.asList(1,2,3,4)); // Arrays.asList()
```



#### ArrayList element 추가/수정/삭제

```java
ArrayList<String> colors = new ArrayList<>();

colors.add("black"); // [black]
colors.add("blue"); // [black, blue]
colors.add(0,"red"); // [red, black, blue]

colors.set(0,"white"); // [white, black, blue]

colors.remove(0); // [black, blue]
colors.remove("blue"); // [black]
```

- add()
  - 기본적으로 리스트의 **가장 끝에 값을 추가**한다
  - 별도로 **인덱스 지정 시 해당 인덱스에 값이 추가되고 그 인덱스부터 값들이 1칸씩 밀린다**
- set()
  - **인덱스 번호에 해당하는 값을 변경한다**
- remove()
  - **인덱스나 element를 입력하여 삭제한다**
- clear()
  - ArrayList 안의 내용 **전체 삭제를 할 때 사용한다**



#### ArrayList 순회 방법

```java
import java.util.*;

public class ArrayListTest {
    public static void main(String[] args) {
        ArrayList<String> colors = new ArrayList<>(Arrays.asList("black","white","green","red"));
        
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



##### **for문과 for-each문 속도**

- for-each문은 내부적으로 iterator가 동작하기 때문에 for문 보다 속도가 느리다



Iterator는 자동으로 Index 관리를 해주기 때문에 사용하는데 편리하지만, Iterator를 열어보면 객체를 만들어서 사용하기 때문에 **느리다**

> list의 size를 받아와서 for loop를 실행하는 것이 더 효율적이다



ListIterator는 **Iterator를 상속받아서 기능을 추가한 것**으로 컬렉션의 요소에 접근할 때 Iterator는 단방향으로만 이동하는 반면, **ListIterator는 양방향으로의 이동이 가능**하다.

-> 하지만 ArrayList니 LinkedList와 같이 List 인터페이스를 구현한 컬렉션에서만 사용할 수 있다.



#### ArrayList 값 존재 유무 확인

```java
ArrayList<String> colors = new ArrayList<>(Arrays.asList("black","white","red"));

boolean conatins = colors.contains("black"); // true

int index = colors.indexOf("white"); // 1

index = colors.indexof("blue"); // -1
```

- contains() - 값 존재 여부 확인 (true/false)
- indexOf() - 값이 있는 경우 인덱스 리턴, 없으면 -1 리턴





참고 : https://psychoria.tistory.com/765