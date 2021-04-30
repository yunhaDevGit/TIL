# LinkedList vs ArrayList



![img](https://www.nextree.co.kr/content/images/2021/01/jdchoi_20140225_arrayvslinkedlist11.png)



ArrayList는 데이터들이 순서대로 늘어선 배열의 형식을 가진다.

LinkedList는 순서대로 늘어선 것이 아니라 자료의 주소 값으로 서로 연결되어 있는 구조를 하고 있다.



ArrayList는 O(N)만큼의 연산 속도가 걸리기 때문에 자료의 최대 개수에 영향을 받지만, LinkedList는 그런 제약을 받지 않는다. 

또한 LinkedList는 개수 제한 없이 자료를 삽입할 수 있는 반면(메모리 용량이 무한하다고 가정할 때), ArrayList는 크기가 한정되어 있어 결국 포화 상태에 이른다. ArrayList의 크기를 재조정하는 연산을 수행하여 크기를 늘릴 수 있지만, 상당한 연산량이 요구된다.





## ArrayList와 LinkedList의 삽입/삭제

#### ArrayList 삽입

<img src="https://t1.daumcdn.net/cfile/tistory/991E4D335B1CF09A23" alt="img" style="zoom:70%;" />

1. 삽입할 자료만큼 공간을 늘리는 작업을 한다.
2. 삽입 될 자료의 위치를 기준으로 기존의 데이터들을 뒤로 혹은 앞으로 이동하는 연산을 한다.
3. 해당 위치에 자료를 입력한 후 삽입 연산을 마친다.



:arrow_right: **ArrayList**는 **사이즈가 고정**되어 있기 때문에 **삽입 시 사이즈를 늘려주는 연산이 추가**되어야 한다.

​	이러한 부가적인 연산은 성능 저하로 이어져 빈번한 삽입이 발생하는 프로세스의 경우 매우 치명적이다.



#### LinkedList 삽입

<img src="https://t1.daumcdn.net/cfile/tistory/997C693C5B1CF1362B" alt="img" style="zoom:80%;" />

1. 추가될 자료의 node를 생성한다.
2. 추가될 자료의 인덱스 이전 node와 추가될 자료 인덱스 이후 node를 설정한다.
3. 추가될 node의 다음 node를 인덱스 이전 node의 다음 node로 설정한다.



:arrow_right: 뒤로 밀리거나 채우는 작업 없이 단지 주소만 서로 연결시켜 주면 되기 때문에 추가/삭제가 ArrayList보다 빠르고 용이하다





#### ArrayList 삭제

<img src="https://t1.daumcdn.net/cfile/tistory/99C515395B1CF0FD37" alt="img" style="zoom:80%;" />

1. 삭제될 자료가 위치한 인덱스의 자료를 삭제한다.
2. 삭제한 자료의 인덱스를 기준으로 이후의 자료들을 이동하는 연산을 수행한다
3. List의 맨 마지막은 비어 있는 상태로 삭제를 완료한다



:arrow_right: **ArrayList**는 **삭제 시 순차적인 인덱스 구조로 인해 삭제된 빈 인덱스를 채워야 하기 때문에 채워주는 연산이 추가 되어야 한다**

​	이러한 부가적인 연산은 성능 저하로 이어져 빈번한 삭제가 발생하는 프로세스의 경우 매우 치명적이다.

:arrow_right: **지속적으로 삭제되는 과정에서 ArrayList는 그 공간만큼 메모리 낭비**를 하게 된다.



#### LinkedList 삭제

![img](https://t1.daumcdn.net/cfile/tistory/992C70365B1CF1BF33)

1. 삭제할 node(50)의 이전 node(40)의 다음 node를 삭제할 node(50)의 다음 node(60)로 설정한다



:arrow_right: 뒤로 밀리거나 채우는 작업 없이 단지 주소만 서로 연결시켜 주면 되기 때문에 추가/삭제가 ArrayList보다 빠르고 용이하다





## ArrayList와 비교한 LinkedList의 장단점

#### 장점

- 자료의 삽입과 삭제가 용이하다
- 리스트 내에서 자료의 이동이 필요하지 않다
- 사용 후 기억 장소의 재사용이 가능하다
- 연속적인 기억 장소의 할당이 필요하지 않다



#### 단점

- 포인터의 사용으로 인해 저장 공간의 낭비가 있다
- 알고리즘이 복잡하다
- 특정 자료의 탐색 시간이 많이 소요된다





> **순차적으로 추가/삭제하는 경우** 
>
> - ArrayList가 더 빠르다
>
> **중간에 데이터를 추가/삭제하는 경우**
>
> - LinkedList가 더 빠르다
>
>   
>
> **->** **다루고자 하는 데이터의 개수가 변하지 않는 경우라면, ArrayList가 최상의 선택이지만, 데이터의 개수의 변경이 잦다면 LinkedList를 사용하는 것이 더 낫다**





참조 : https://shlee0882.tistory.com/95

​		   https://www.nextree.co.kr/p6506/