# JSON과 메서드

네트워크를 통해 객체를 어딘가에 보내거나 로깅 목적으로 객체를 출력해야 한다면 객체를 문자열로 전환해야 합니다. 

이 때 전환된 문자열엔 원하는 정보가 있는 객체 프로퍼티가 모두 포함되어야만 합니다. 

아래 예시처럼 메서드를 구현해 객체를 문자열로 전환할 수 있습니다.

```jsx
let user = {
	name: "John",
	age: 30

	toString() {
		return `{name: "${this.name}", age: ${this.age}}`;
	}
};

alert(user); // {name: "John", age: 30}
```

그러나 개발 과정에서 프로퍼티가 추가되거나 삭제, 수정이 될 경우 `toString`을 매번 수정해야 합니다. 

이러한 불편함을 줄이기 위해 자바스크립트에선 관련 기능을 구현해주었습니다. 

### JSON.stringify

JSON(Javascript Object Notation)은 값이나 객체를 나타내는 범용 포맷으로 본래 자바스크립트에서 사용할 목적으로 만들어진 포맷입니다. 그러나 라이브러리를 통해 자바스크립트가 아닌 언어에서도 JSON을 충분히 다룰 수 있어 JSON을 데이터 교환 목적으로 사용하는 경우가 많습니다.

자바스크립트가 제공하는 JSON 관련 메서드는 아래와 같습니다. 

- `JSON.stringify` - 객체를 JSON으로 바꿔줍니다.
- `JSON.parse` - JSON을 객체로 바꿔줍니다.

객체 `student`에 `JSON.stringify`를 적용해봅시다.

```jsx
let student = {
  name: 'John',
  age: 30,
  isAdmin: false,
  courses: ['html', 'css', 'js'],
  wife: null
};

let json = JSON.stringify(student);

alert(typeof json); // string

alert(json);
/* JSON으로 인코딩된 객체:
{
  "name": "John",
  "age": 30,
  "isAdmin": false,
  "courses": ["html", "css", "js"],
  "wife": null
}
*/
```

`JSON.stringify(student)`를 사용해 변경된 문자열은 *JSON으로 인코딩된(JSON-encoded), 직렬화 처리된(Serialized), 문자열로 변환된(Stirngified), 결집된(marshalled) 객체라고* 부릅니다. 객체는 이렇게 문자열로 변환된 후에 네트워크를 통해 전송하거나 저장소에 저장할 수 있습니다. 

JSON으로 인코딩된 객체는 일반 객체와 다른 특징을 보입니다. 

- 문자열은 **큰따옴표**로 감싸야 합니다. JSON에선 작은 따옴표나 백틱을 사용할 수 없습니다.
- 객체 프로퍼티 이름은 **큰 따옴표**로 감싸야 합니다.

`JSON.stringify`는 객체 뿐만 아니라 원시값에도 적용할 수 있습니다. 

적용할 수 있는 자료형은 아래와 같습니다. 

- 객체 `{...}`
- 배열 `[...]`
- 원시형:
    - 문자형
    - 숫자형
    - 불린형 값 `true`와 `false`
    - `null`
    

```jsx
// 숫자를 JSON으로 인코딩하면 숫자입니다.
alert( JSON.stringify(1) ) // 1

// 문자열을 JSON으로 인코딩하면 문자열입니다(다만, 큰따옴표가 추가됩니다).
alert( JSON.stringify('test') ) // "test"

alert( JSON.stringify(true) ); // true

alert( JSON.stringify([1, 2, 3]) ); // [1,2,3]
```

JSON은 데이터 교환을 목적으로 만들어진 언어이므로 자바스크립트 특유의 객체 프로퍼티는 `JSON.stringify`가 처리할 수 없습니다. 

- 함수 프로퍼티(메서드)
- 심볼형 프로퍼티 (키가 심볼인 프로퍼티)
- 값이 `undefined`인 프로퍼티

```jsx
let user = {
  sayHi() { // 무시
    alert("Hello");
  },
  [Symbol("id")]: 123, // 무시
  something: undefined // 무시
};

alert( JSON.stringify(user) ); // {} (빈 객체가 출력됨)
```

`JSON.stringify`는 중첩 객체도 알아서 문자열로 바꿔줍니다. 

```jsx
let meetup = {
  title: "Conference",
  room: {
    number: 23,
    participants: ["john", "ann"]
  }
};

alert( JSON.stringify(meetup) );
/* 객체 전체가 문자열로 변환되었습니다.
{
  "title":"Conference",
  "room":{"number":23,"participants":["john","ann"]},
}
*/
```

### replacer로 원하는 프로퍼티만 직렬화하기

`JSON.stringify`의 전체 문법은 아래와 같습니다. 

```jsx
let json = JSON.stringify(value[, replacer, space]);
```

**value**

- 인코딩 하려는 값

**replacer**

- JSON으로 인코딩 하길 원하는 프로퍼티가 담긴 배열 또는 매핑 함수 `function(key, value)`

**space**

- 서식 변경 목적으로 사용할 공백 문자 수

 JSON으로 변환하길 원하는 프로퍼티가 담긴 배열을 두 번째 인수로 넘겨주면 이 프로퍼티들만 인코딩할 수 있습니다.

```jsx
let room = {
  number: 23
};

let meetup = {
  title: "Conference",
  participants: [{name: "John"}, {name: "Alice"}],
  place: room // meetup은 room을 참조합니다.
};

room.occupiedBy = meetup; // room references meetup

/*
* 방법 1
*/
alert( JSON.stringify(meetup, ['title', 'participants']) );
// {"title":"Conference","participants":[{},{}]}

/*
* 방법 2
*/
alert( JSON.stringify(meetup, ['title', 'participants', 'place', 'name', 'number']) );
/*
{
  "title":"Conference",
  "participants":[{"name":"John"},{"name":"Alice"}],
  "place":{"number":23}
}
*/
```

배열에 `name`을 넣지 않아서 `participants`가 비어버렸습니다. 넣어주니 제대로 잘 출력합니다.

`replacer` 에 전달되는 함수(`replacer`함수)는 프로퍼티 `(키, 값)` 쌍 전체를 대상으로 호출되는데, 반드시 기존 프로퍼티 값을 대신하여 사용할 값을 반환해야 합니다. 

### space로 가독성 높이기

`JSON.stringify(value, replacer, space)`의 세 번째 인수 `space`는 가독성을 높이기 위해 중간에 삽입해 줄 공백 문자 수를 나타냅니다. 

지금까진 `space` 없이 메서드를 호출했기 때문에 인코딩된 JSON에 들여쓰기나 여분의 공백 문자가 하나도 없었습니다. `space`는 가독성을 높이기 위한 용도로 만들어졌기 때문에 단순 전달 목적이라면 `space`없이 직렬화 합니다. 

```jsx
let user = {
  name: "John",
  age: 25,
  roles: {
    isAdmin: false,
    isEditor: true
  }
};

alert(JSON.stringify(user, null, 2));
/* 공백 문자 두 개를 사용하여 들여쓰기함:
{
  "name": "John",
  "age": 25,
  "roles": {
    "isAdmin": false,
    "isEditor": true
  }
}
*/

/* JSON.stringify(user, null, 4)라면 아래와 같이 좀 더 들여써집니다.
{
    "name": "John",
    "age": 25,
    "roles": {
        "isAdmin": false,
        "isEditor": true
    }
}
*/
```

### 커스텀 “toJSON”

`toString`을 사용해 객체를 문자형으로 변환 시키는 것처럼, 객체에 `toJSON`이라는 메서드가 구현되어 있으면 객체를 JSON으로 바꿀 수 있습니다. `JSON.stringify`는 이런 경우를 감지하고 `toJSON`을 자동으로 호출해줍니다. 

```jsx
let room = {
	number: 23
};

let meetup = {
	title: "Conference",
	date: new Date(Date.UTC(2017, 0, 1)),
	room
};

alert (JSON.stringify(meetup));
/*
{
	"title":"Conference",
    "date":"2017-01-01T00:00:00.000Z",  // (1)
    "room": {"number":23}               // (2)
  }
*/
```

Date 객체의 내장 메서드 `toJSON`이 호출되면서 `date`의 값이 문자열로 변환되었습니다. 

`room`에 직접 커스텀 메서드 `toJSON`을 추가해보겠습니다. 이때 (2) 줄이 어떻게 변경되는지 봅시다.

```jsx
let room = {
  number: 23,
  toJSON() {
    return this.number;
  }
};

let meetup = {
  title: "Conference",
  room
};

alert( JSON.stringify(room) ); // 23

alert( JSON.stringify(meetup) );
/*
  {
    "title":"Conference",
    "room": 23
  }
*/
```

위와 같이 `toJSON`은 `JOSN.stringify(room)`을 직접 호출할 때도 사용할 수 있고, `room`과 같은 중첩 객체에도 구현하여 사용할 수 있습니다. 

### JSON.parse

JSON.parse를 사용하면 JSON으로 인코딩 된 객체를 다시 객체로 디코딩 할 수 있습니다.

```jsx
let value = JSON.parse(str, [reviver]);
```

**str**

- JSON 형식의 문자열

**reviver**

- 모든 (key, value) 쌍을 대상으로 호출되는 function(key, value) 형태의 함수로 값을 변경 시킬 수 있습니다.

```jsx
let numbers = "[0, 1, 2, 3]";
numbers = JSON.parse(numbers);
alert( numbers[1]);
```

추가로 JSON은 주석을 지원하지 않습니다. 주석을 추가하면 유효하지 않은 형식이 됩니다. 

### reviver 사용하기

서버로부터 전송 받은 문자열이 아래와 같은 형식이라고 합시다.

```jsx
// title: (meetup 제목), date: (meetup 일시)
let str = '{"title":"Conference","date":"2017-11-30T12:00:00.000Z"}';
```

이 문자열을 *역 직렬화(deserialize)*해서 자바스크립트 객체로 만들어봅시다

`JSON.parse`를 호출해봅니다.

```jsx
let str = '{"title":"Conference","date":"2017-11-30T12:00:00.000Z"}';

let meetup = JSON.parse(str);

alert( meetup.date.getDate() ); // 에러!
```

에러가 발생합니다.

`meetup.date`의 값은 `Date`객체가 아니고 문자열이기 때문에 발생한 에러입니다. 

문자열을 `Date`로 전환해줘야 한다는 것을 어떻게 `JSON.parse`가 알 수 있을까요?

이럴 때 `JSON.parse`의 두 번째 인수 `reviver`를 사용하면 됩니다. 모든 값은 “그대로”, 하지만 `date`는 `Date` 객체를 반환하도록 함수를 구현해 봅시다.
```javascript
let str = '{"title":"Conference","date":"2017-11-30T12:00:00.000Z"}';

let meetup = JSON.parse(str, function(key, value) {
  if (key == 'date') return new Date(value);
  return value;
});

alert( meetup.date.getDate() ); 
```
