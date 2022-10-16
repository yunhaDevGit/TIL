# instanceof

- instanceof : 참조변수가 참조하고 있는 인스턴스의 실제 타입을 알아보기 위해 사용하는 연산자
- instanceof의 왼쪽에는 참조변수를, 오른쪽에는 타입(클래스명)이 피연산자로 위치
- 연산의 결과로 boolean값인 true와 false 중의 하나를 반환
- instanceof를 이용한 연산결과로 true를 얻었다는 것은 참조변수가 검사한 타입으로 형변환이 가능하다는 것
+) 값이 null인 참조변수에 대해 instanceof연산을 수행하면 false를 결과로 얻음

```java
void doWork(Car c) {
       if(c instanceof FireEngine) { //1.형변환이 가능한지 확인
    	FireEngine fe = (FireEngine)c; //2.형변환
        fe.water();
        ...
```
위의 코드는 Car타입의 참조변수 c를 매개변수로 하는 메서드이다


instanceof 연산자로 참조변수 c가 가리키고 있는 인스턴스의 타입을 체크하고, 적절히 형변환한 다음에 작업해야 한다

조상타입의 참조변수로는 실제 인스턴스의 멤버들을 모두 사용할 수 없기 때문에 실제 인스턴스와 같은 타입의 참조변수로 형변환을 해야만 인스턴스의 모든 멤버들을 사용 가능

