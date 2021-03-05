# TDD(Test-driven Development)

TDD란?

- 테스트 주도 개발로 매우 짧은 개발 사이클을 반복하는 소프트웨어 개발 프로세스
- 바라는 향상 또는 새로운 함수를 정의하는 자동화된 테스트 케이스를 작성하고, 그 케이스를 통과하기 위한 최소한의 코드 양을 생성하고, 그 새 코드를 표준에 맞도록 리팩토링한다. 

![image-20210305184017077](C:\Users\The Jeong\AppData\Roaming\Typora\typora-user-images\image-20210305184017077.png)

사실 테스트 코드를 만드는 순서 보다는 각각의 작은 메서드마다 테스트 코드를 작성하고 모든 단위를 작게 쪼개 검증해나가면서 테스트 코드를 작성하는 것이 중요한 것 같다. 

작은 단위로 쪼개서 검증해나가면서 코딩을 하면 어디서 문제가 생기고 어떤 문제인지 좀 더 쉽고 빠르게 개발 할 수 있을 것 같다. 



#### TDD의 장점

1. 객체지향적인 코드 개발
2. 설계 수정 시간의 단축
3. 디버깅 시간의 단축
4. 유지 보수의 용이성
5. 테스트 문서의 대체 가능



#### Intellij에서 TDD 하기

##### Test Class 생성

테스트 케이스를 만들고 싶은 클래스에서 **Ctrl + Shift + t**



##### Test Code 작성

다음과 같은 방식으로 테스트 코드를 작성할 수 있다.

![img](https://lh3.googleusercontent.com/icCWGpXlGIaV8Xi-f36YGOfUMjP0Z7bmbgJp3XOKCIbgoMJfnTsK252YURxJ5CGh1qGaEklG1mYh-6MqVYl8o8E2JbzClrsRyHujMd8TZS_c_IXQSX9oTLFaLDrZk1uzCk3W5BqI)

- Given : 파라미터, 변수 선언
- When : 로직 구현
- Then : 검증(실제 결과가 어떻게 나오는지)



@SpringBootTest : autowired로 bean을 주입해서 쓰니까 이 어노테이션을 써서 스프링 컨테이너를 실행시키겠다고 생각하면 된다. 



Test 코드를 작성할 때, given, when, then 규칙을 사용하여 작성하는 것이 좋은데, 항상 저 형태를 작성하기 번거로우니까 live template을 통해 자동으로 작성될 수 있게끔 template을 만들 수 있다. 

<img src="https://lh5.googleusercontent.com/D6Sj0EbVuo1VwlADMUKHTR1z4A3HP2m0NH_JP_kZEiqYwMPKOQl21id7cLt1bFEzb0qmiK3-2DqCpmSzApzQQUSfyxCStv7k25lTdndRjjNezZGg54171sBry_mcDhXFYqf0dIQQ" alt="img" style="zoom:70%;" />

**Setting -> Editor -> Live Template -> + Template Group -> + Live Template -> define(전부 다) -> 원하는 형식 작성**



앞으로 Test Code를 작성하는 습관을 들이자!

 