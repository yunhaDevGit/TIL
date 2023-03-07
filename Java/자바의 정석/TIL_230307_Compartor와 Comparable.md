# Comparator와 Comparable

- 객체 정렬에 필요한 메서드(정렬 기준)을 정의한 인터페이스

```java
Comparable : 기본 정렬 기준을 구현하는데 사용
Comparator : 기본 정렬 기준 외에 다른 기준으로 정렬하고자 할 때 사용

public interface Comparator {
	int compare(Object o1, Object o2); // o1, o2 두 객체를 비교
	boolean equals(Object obj); // equals를 오버라이딩하라는 뜻
}

public interface Comparable {
	int compareTo(Object o); // 주어진 객체(o)를 자신과 비교
}
```

- compare(0와 compareTo()는 두 객체의 비교 결과를 반환하도록 작성
    
    ```java
    public final class Integer extends Number implements Comparable {
    	...
    	public int compareTo(Integer anotherInteger) {
    		int v1 = this.value;
    		int v2 = anotherInteger value;
    		// 같으면 0, 오른쪽이 크면 -1, 작으면 1 반환
    		return( (v1 < v2) ? -1 : (v1==v2)? 0 : 1))
    ```
    
    
