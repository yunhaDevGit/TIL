# List.sunList

아래와 같이 subList를 이용하여 List를 자를 수 있다.
```java
List<Integer> list  = Lists.newArrayList(1,2,3,4,5,6,7,8,9);
System.out.println(list.subList(0,3);
```
+) fromIndex는 포함. toIndex는 제외
```java
list.subList(0,3); // index 0부터 index 2까지
```


하지만 subList를 사용할 경우 발생할 수 있는 두 가지 문제가 있다.
우선 subList의 소스 코드를 보자

```java
public list<E> subList(int fromIndex, int toIndex){
	subListRangeCheck(fromIndex, toIndex, size);
	return new SubList(this,0,fromIndex,toIndex);
}

static void subListRangeCheck(int fromIndex, int toIndex, int size){
	if(fromIndex < 0)
		throw new IndexOutOfBoundsException("fromIndex = " + fromIndex);  
	if(toIndex > size)  
		throw  new  IndexOutOfBoundsException("toIndex = " + toIndex);  
	if(fromIndex > toIndex)
		throw  new  IllegalArgumentException("fromIndex(" + fromIndex + ") > toIndex(" + toIndex + ")");
}

private  class  SubList  extends  AbstractList<E>  implements  RandomAccess  {  
	private final AbstractList<E> parent;  
	private final int parentOffset;  
	private final int offset; 
	int size;
```

ArrayList의 subList는 부모 List를 가진다.
**여기서 두 가지 문제가 발생할 수 있다.**

**1. 캐시 사용 시 불필요한 메모리 점유**
 - 10000개의 list에서 10개만 subList로 만들고 이를 캐시에 저장할 경우, 위의 구현 방식으로 인해 10개의 데이터만 캐싱되는 것이 아닌 10000개의 리스트 값들이 모두 캐시 되게 된다. 즉, 불필요한 메모리를 점유하게 된다.
	
**2. 부모 list가 변경될 경우 subList에 영향**

```java	
List<Integer> list = Lists.newArrayList(1,2,3,4,5,6,7,8,9);
List<Integer> subList = list.subList(0,3);
System.out.println(subList);
list.remove(0);
System.out.println(subList);
```	
	
```java	
[1,2,3]
Exception in thread "main" java.util.ConcurrentModificationException at 
	java.util.ArrayList$SubList.checkForComodification(ArrayList.java:1231) at 
	java.util.ArrayList$SubList.listIterator(ArrayList.java:1091) at 
	java.util.AbstractList.listIterator(AbstractList.java:299) at 
	java.util.ArrayList$SubList.iterator(ArrayList.java:1087)
```	

즉, subList를 무분별하게 사용하지 말고, 따로 list를 생성하는 경우 잘 고민 후에 사용하자!

```java
List<Integer> subList = new ArrayList<>(list.subList(0,3));
```


참고 : https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=yjw2288&logNo=221072561904
