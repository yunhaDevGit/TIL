# 메서드와 this

객체는 사용자(user), 주문(order) 등과 같이 실제 존재하는 개체(entity)를 표현하고자 할 때 생성됩니다. 

사용자는 현실에서 장바구니 담기, 물건 선택하기, 로그인하기 등의 행동을 합니다. 이와 마찬가지로 사용자를 나타내는 객체도 *특정한 행동*을 할 수 있습니다.

자바스크립트에서는 객체의 프로퍼티에 함수를 할당하여 객체에게 행동할 수 있는 능력을 부여합니다.

### 메서드 만들기

```jsx
let user = {
  name: "John",
  age: 30
};

user.sayHi = function() {
  alert("안녕하세요!");
};

user.sayHi(); // 안녕하세요!
```

함수 표현식으로 함수를 만들고, 객체 프로퍼티 `user.sayHi`에 함수를 할당해 주었습니다. 

이렇게 객체 프로퍼티에 할당된 함수를 *메서드(method)*라고 부릅니다. 

아래와 같이 이미 정의된 함수를 이용해서 만들 수도 있습니다.

```jsx
let user = {
  // ...
};

// 함수 선언
function sayHi() {
  alert("안녕하세요!");
};

// 선언된 함수를 메서드로 등록
user.sayHi = sayHi;

user.sayHi(); // 안녕하세요!
```

### 메서드 단축 구문

객체 리터럴 안에 메서드를 선언할 때 사용할 수 있는 단축 문법은 아래와 같습니다. 

```jsx
// 일반 메서드 선언
user = {
  sayHi: function() {
    alert("Hello");
  }
};

// 단축 구문 사용
user = {
  sayHi() { // "sayHi: function()"과 동일합니다.
    alert("Hello");
  }
};
```

위와 같이 `function`을 생략하여 메서드 정의를 할 수 있습니다. 

### 메서드와 this

메서드 내부에서 `this` 키워드를 사용하면 객체에 접근할 수 있습니다. 

이때 ‘점 앞’의 `this`는 객체를 나타냅니다. 정확히는 메서드를 호출할 때 사용된 객체를 나타냅니다. 

```jsx
let user = {
  name: "John",
  age: 30,

  sayHi() {
    // 'this'는 '현재 객체'를 나타냅니다.
    alert(this.name);
  }
};

user.sayHi(); // John
```

`this`를 사용하지 않고 외부 변수를 참조해 객체에 접근하는 것도 가능합니다. 

```jsx
let user = {
  name: "John",
  age: 30,

  sayHi() {
    alert(user.name); // 'this' 대신 'user'를 이용함
  }
};
```

하지만 외부 변수를 사용하여 객체를 참조할 경우 예상치 못한 에러가 발생할 수 있습니다.

만약 user를 복사해 다른 변수에 할당(admin=user)하고, user는 전혀 다른 값으로 덮어썼다고 할 경우 sayHi()는 null을 참조할 것입니다. 

```jsx
let user = {
  name: "John",
  age: 30,

  sayHi() {
    alert( user.name ); // Error: Cannot read property 'name' of null
  }
};

let admin = user;
user = null; // user를 null로 덮어씁니다.

admin.sayHi(); // sayHi()가 엉뚱한 객체를 참고하면서 에러가 발생했습니다.
```

### 자유로운 this

자바스크립트에선 모든 함수에 `this`를 사용할 수 있습니다. 

아래와 같이 코드를 작성해도 문법 에러가 발생하지 않습니다.

```jsx
function sayHi() {
	alert( this.name );
}
```

`this` 값은 런타임에 결정됩니다. 동일한 함수라도 다른 객체에서 호출했다면 `this`가 참조하는 값이 달라집니다.
