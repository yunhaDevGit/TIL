# Generics

- 컴파일 시 타입을 체크해주는 기능(compile-time type check)

    ```java
    //
    ArrayList<Tv> tvList = new ArrayList<Tv>();
    
    tvList.add(new Tv());
    tvList.add(new Audio()); // 컴파일 에러(Tv외의 다른 타입은 저장 불가)터타
    ```


## 타입변수

- 클래스를 작성할 때, Object 타입 대신 타입 변수(E)를 선언해서 사용

    ```java
    // 예전
    public class ArrayList extends AbstractList {
    	private transient Object[] elementData;
    	public boolean add(Object o) { /* */ }
    	public Object get(int index) { /* */ }
    	...
    }
    
    // 변경
    public class ArrayList<E> extends AbstractList<E> {
    	private transient E[] elementData;
    	public boolean add(E o) { /* */ }
    	public E get(int index) { /* */ }
    	...
    }
    ```


## 타입변수에 대입하기

- 객체 생성시, 타입 변수(E) 대신 실제 타입(Tv)을 지정(대입)

```java
ArrayList<Tv> tvList = new ArrayList<Tv>();

public class ArrayList<Tv> extends AbstractList<E> {
	private transient Tv[] elementData;
	public boolean add(Tv o) { /* */ }
	public Tv get(int index) { /* */ }
	...
}
```

- 타입 변수 대신 실제 타입이 지정되면, 형 변환 생략 가능

## 제네릭 용어

```java
Box<T> 제네릭 클래스, T의 Box 또는 T Box
T      타입 변수 또는 타입 매개변수
Box    원시 타입(raw type)
```

## 제네릭 타입과 다형성

- 참조 변수와 생성자의 대입된 타입은 일치해야 한다

```java
ArrayList<Tv> list = new ArrayList<Tv>(); // OK
ArrayList<Product> list = new ArrayList<Tv>(); // Error
```

- 제네릭 클래스 간의 다형성은 성립(여전히 대입된 타입은 일치해야 함)

```java
List<Tv> list = new ArrayList<Tv>(); // OK
List<Tv> list = new LinkedList<Tv>(); // OK
```

- 매개변수의 다형성도 성립

```java
ArrayList<Producst> list = new ArrayList<Product>();
list.add(new Product());
list.add(new Tv());
list.add(new Audio());
```

## Iterator<E>

- 클래스를 작성할 때, Object 타입 대신 T와 같은 타입 변수 사용

## HashMap<Key>

- 여러 개의 타입 변수가 필요한 경우 콤마(,)를 구분자로 선언

```java
public V put(K key, V value) { /* */ }
```

## 제한된 제네릭 클래스

- extends로 대입할 수 있는 타입을 제한

```java
class FruitBox<T extends Fruit> {
	ArrayList<T> list = new ArrayList<T>();
	...
}

FruitBox<Apple> appleBox = new FruitBox<Apple>(); // Ok
FruitBox<Toy> toyBox = new FruitBox<Toy>(); // Error
```

- 인터페이스인 경우에도 **extends 사용**

```java
interface Eatable {}
class FruitBox<T extends Eatable> { ... }
```

## 제네릭의 제약

- 타입 변수에 대입은 인스턴스 별로 다르게 가능

```java
Box<Apple> appleBox = new Box<Apple>(); // Ok
Box<Grape> grapdBox = new Box<Grape>(); // Ok
```

- static 멤버에 타입 변수 사용 불가

```java
class Box<T> {
	static T item;
	static int compare(T t1, T t2) { ... } // error
```

- 배열 생성할 때 타입 변수 사용 불가, 타입 변수로 배열 선언은 가능

```java
class Box<T> {
	T[] itemArr; // ok. T 타입 배열을 위한 참조 변수
	...
	T[] toArray() {
		T[] tmpArr = new T[itemArr.length]; // error. 제네릭 배열 생성 불가
		...
	}
}
```

## 와일드 카드 <?>

- 하나의 참조 변수로 대입된 타입이 다른 객체를 참조 가능

```java
ArrayList<? extends Product> list = new ArrayList<Tv>();
ArrayList<? extends Product> list = new ArrayList<Audio>();
ArrayList<Product> list = new ArrayList<Tv>(); // error

<? extends T> - 와일드 카드의 상한 제한. T와 그 자손들만 가능
<? super T>   - 와일드 카드의 하한 제한. T와 그 조상들만 가능
<?>           - 제한 없음. 모든 타입이 가능. <? extends Object>와 동일
```

- 메서드의 매개변수에 와일드 카드 사용

```java
static Juice makeJuice(FruitBox<? extends Fruit> box) {
	String tmp = "";
	for(Fruit f: box.getList()) tmp += f + " ";
	return new Juice(tmp);
}
```

## 제네릭 메서드

- 제네릭 타입이 선언된 메서드(타입 변수는 메서드 내에서만 유효)

```java
static <T> void sort(List<T> list, Comparator<? super T> c)
```

- 클래스의 타입 매개변수 <T> 와 메서드의 타입 매개변수 <T>는 별개

```java
class FruitBox<T> { // 제네릭 클래스
	...
	static <T> void sort(List<T> list, Comparator<? super T> c) {
		...
	}
}
```

- 메서드를 호출할때마다 타입을 대입해야(대부분 생략 가능)

```java
FruitBox<Fruit> fruitBox = new FruitBox<Fruit>();
FruitBox<Apple> appleBox = new FruitBox<Apple>();

...
System.out.println(Juicer.<Fruit>makeJuice(fruitBox));
System.out.println(Juicer.<Apple>makeJuice(appleBox));

static <T extends Fruit> Juice makeJuice(FruitBox<T> box) {
	String tmp = "";
	for(Fruit f: box.getList()) tmp += f + " ";
	return new Juice(tmp);
}
```

- 메서드를 호출할 때 타입을 생략하지 않을 때는 클래스 이름 생략 불가

```java
System.out.println(<Fruit>makeJuice(fruitBox)); // Error. 클래스 이름 생략 불가
System.out.println(this.<Fruit>makeJuice(fruitBox)); // OK
System.out.println(Juicer.<Fruit>makeJuice(fruitBox)); // OK
```

## 제네릭 타입의 형변환

- 제네릭 타입과 원시 타입 간의 형변환은 바람직하지 않다(경고 발생)

```java
Box<Object> objBox = null;
Box box = (Box)objBox; // Ok. 제네릭 타입 -> 원시 타입 (경고 발생)
objBox = (Box<Object>)box; // Ok. 원시 타입 -> 제네릭 타입 (경고 발생)

objBox = (Box<Object>)strBox; // error. Box<String> -> Box<Object> 
strBox = (Box<String>)objBox; // error. Box<Object> -> Box<String> 
```

- 와일드 카드가 사용된 제네릭 타입으로는 형 변환 가능

```java
Box<Object> objBox = (Box<Object>)new Box<String>(); // error. 형변환 불가
Box<? extends Object> wbox = (Box<? extends Object>)new Box<String>(); // Ok.
Box<? extends Object> wBox = new Box<String>(); // 위 문장과 동일
```

## 제네릭 타입의 제거

- 컴파일러는 제네릭 타입을 제거하고, 필요한 곳에 형변환을 넣는다
    - 제네릭 타입의 경계(bound)를 제거

      (하위 호환성 때문..제네릭 타입이 JDK1.5 이후에 나옴 → 이전 버전과도 호환되게 하기 위해 컴파일 타임에 제네릭 타입을 제거)

    - 제네릭 타입 제거 후에 타입이 불일치하면, 형 변환 추가
    - 와일드 카드가 포함된 경우, 적절한 타입으로 형변환 추가