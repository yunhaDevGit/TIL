# instanceof

- 참조 변수의 형 변환 가능 여부 확인에 사용한다. 가능하면 `true`를 반환한다.
    - 연산의 결과로 boolean값인 true와 false 중의 하나를 반환
- 형 변환 전에 반드시 `instanceof`로 확인해야 한다.
    
    ```java
    void doWork(Car c) { // Car 또는 car의 모든 자손이 들어올 수 있다
    	if(c instanceof FireEngine) { // 1.형변환이 가능한지 확인
    		FireEngine fe = (FireEngine)c; // 2.형변환
    		fe.water();
        ...
    }
    // 형 변환을 하는 이유는 인스턴스의 원래 기능을 모두 사용하려고 하는 것이다.
    // Car 타입의 리모콘인 c로는 water()를 호출할 수 없으니까 리모콘을 FireEngine 타입으로 바꿔서 water()를 호출한다. 
    ```
    
- instanceof의 왼쪽에는 참조 변수를, 오른쪽에는 타입(클래스명)이 피연산자로 위치
- instanceof를 이용한 연산 결과로 true를 얻었다는 것은 참조 변수가 검사한 타입으로 형 변환이 가능하다는 것
+) 값이 null인 참조 변수에 대해 instanceof연산을 수행하면 false를 결과로 얻음

위의 코드는 Car타입의 참조 변수 c를 매개변수로 하는 메서드이다

instanceof 연산자로 참조 변수 c가 가리키고 있는 인스턴스의 타입을 체크하고, 적절히 형 변환한 다음에 작업해야 한다

조상 타입의 참조 변수로는 실제 인스턴스의 멤버들을 모두 사용할 수 없기 때문에 실제 인스턴스와 같은 타입의 참조 변수로 형 변환을 해야만 인스턴스의 모든 멤버들을 사용 가능
