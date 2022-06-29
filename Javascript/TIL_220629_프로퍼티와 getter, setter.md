# 프로퍼티와 getter, setter

객체의 프로퍼티는 두 종류로 나뉩니다.

첫 번째로 *데이터 프로퍼티(data property)*입니다. 지금까지 사용한 모든 프로퍼티는 데이터 프로퍼티입니다.

두 번째는 *접근자 프로퍼티(accessor property)*입니다. 접근자 프로퍼티의 본직은 함수인데, 이 함수는 get하고 set하는 역할을 담당합니다. 

### getter와 setter

접근자 프로퍼티는 ‘getter’와  ‘setter’ 메서드로 표현됩니다. 객체 리터럴 안에서 getter와 setter 메서드는 `get`과 `set`으로 나타낼 수 있습니다. 

```jsx
let obj = {
	get propName() {
		// getter, obj.propName을 실행할 때 실행
	},
	set propName(value) {
		// setter, obj.propNmae = value를 실행할 때 실행
	}
};
```

getter 메서드는 `obj.propName`을 사용해 프로퍼티를 읽으려고 할 때 실행되고, setter 메서드는 `obj.propName = value`로 프로퍼티에 값을 할당하려 할 때 실행됩니다.

프로퍼티 `name`과 `surname`이 있는 객체 `user`의 프로퍼티를 복사-붙여넣기 하지 않고 `fullName`이라는 것을 만들어보겠습니다

```jsx
let user = {
  name: "John",
  surname: "Smith",

  get fullName() {
    return `${this.name} ${this.surname}`;
  }
};

alert(user.fullName); // John Smith
```

밖에선 접근자 프로퍼티를 일반 프로퍼티처럼 사용할 수 있습니다. 

한편, 위의 `fullName`은 getter 메서드만 가지고 있기 때문에 `user.fullName=`을 사용해 값을 할당하려 할 경우 에러가 발생합니다. 

```jsx
let user = {
	get fullName() {
		return `...`;
	}
};

user.fullName = "Test"; // Error
```

위의 코드가 에러 나지 않게 하기 위해서는 setter 메서드를 추가하면 됩니다. 

```jsx
let user = {
  name: "John",
  surname: "Smith",

  get fullName() {
    return `${this.name} ${this.surname}`;
  },

  set fullName(value) {
    [this.name, this.surname] = value.split(" ");
  }
};

// 주어진 값을 사용해 set fullName이 실행됩니다.
user.fullName = "Alice Cooper";

alert(user.name); // Alice
alert(user.surname); // Cooper
```

### 접근자 프로퍼티 설명자

접근자 프로퍼티엔 설명자 `value`와 `writable`가 없는 대신에 `get`과 `set`이라는 함수가 있습니다.

따라서 접근자 프로퍼티는 다음과 같은 설명자를 갖습니다.

- **`get`** – 인수가 없는 함수로, 프로퍼티를 읽을 때 동작함
- **`set`** – 인수가 하나인 함수로, 프로퍼티에 값을 쓸 때 호출됨
- **`enumerable`** – 데이터 프로퍼티와 동일함
- **`configurable`** – 데이터 프로퍼티와 동일함

```java
let user = {
  name: "John",
  surname: "Smith"
};

Object.defineProperty(user, 'fullName', {
  get() {
    return `${this.name} ${this.surname}`;
  },

  set(value) {
    [this.name, this.surname] = value.split(" ");
  }
});

alert(user.fullName); // John Smith

for(let key in user) alert(key); // name, surname
```
