# wrapper, Number 클래스

## Wrapper 클래스

- 8개의 기본형을 객체로 다뤄야 할 때 사용하는 클래스

```java
public final class Integer extends Number implements Comparable {
	...
	private int value;
	...
}
```

![image](https://user-images.githubusercontent.com/74949294/220119288-c5cabacd-d1dc-4682-97fd-d31d9c8a0e8f.png)

- 기본형은 값에 직접 접근이 가능한데, 참조형은 참조 변수를 통해서만 접근이 가능
    - 성능 문제 때문에 기본형이 생겼다

## Number 클래스

- 모든 숫자 래퍼 클래스의 조상

![image](https://user-images.githubusercontent.com/74949294/220119316-f0e38eae-673f-4fb5-bcac-23b8ae60a1bc.png)

- 래퍼 객체를 기본형으로 바꿀 때 사용하는 메서드를 가지고 있다
