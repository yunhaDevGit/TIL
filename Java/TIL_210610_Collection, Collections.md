# Collection, Collections 차이

![img](https://blog.kakaocdn.net/dn/HAwYW/btqA3dYwlpO/RrLRRmuhJdceM5yo7EOQo1/img.png)

### Collection - 인터페이스

Collection framework라고 하는 계층의 최상위 인터페이스이다.

Collection framework는 기본적으로 여러 요소를 담기 위해서 만들어졌다. 그래서 컨테이너 객체라고도 불린다.

**Collection 인터페이스를 Set, List, Queue 인터페이스가 구현하고 있다.**

Map은 단순히 값을 배열로 저장하는 Set, List와 다르게 키, 값으로 저장을 하므로 Collection 인터페이스를 구현하고 있지는 않고, **프레임워크에 같이 정의만 되어있다**.


### Collections - 클래스

**Collection을 가지고 놀기 위한 클래스이다**.

다시 말해 Collection 인터페이스를 리턴하거나 Collection 인터페이스에서 동작하는 메소드를 모아 놓은 클래스이다. 

Collections 클래스에는 EMPTY_LIST, EMPTY_SET, EMPTY_MAP이라는 필드가 있는데, 제어자는 각각 List, Set, Map이다.


참고 : https://live-everyday.tistory.com/85
           https://yeon-kr.tistory.com/113
