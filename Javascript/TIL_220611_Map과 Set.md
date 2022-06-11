# Map과 Set

## 맵

맵(Map)은 키가 있는 데이터를 저장한다는 점에서 `객체`와 유사합니다. 다만, `맵`은 키에 다양한 자료형을 허용한다는 점에서 차이가 있습니다. 

맵의 주요 메서드와 프로퍼티는 다음과 같습니다.

- `new Map()` - 맵을 만듭니다.
- `map.set(key, value)` - `key`를 이용해 `value`를 저장합니다.
- `map.get(key)` - `key`에 해당하는 값을 반환합니다. `key`가 존재하지 않으면 `undefined`를 반환합니다.
- `map.has(key)` - `key`가 존재하면 `true`, 존재하지 않으면 `false`를 반환합니다.
- `map.delete(key)` - `key`에 해당하는 값을 삭제합니다.
- `map.clear()` - 맵 안의 모든 요소를 제거합니다.
- `map.size()` - 요소의 개수를 반환합니다.

맵은 객체와 달리 키를 문자형으로 변환하지 않으므로 자료형 제한이 없습니다.

```jsx
let map = new Map();

map.set('1', 'str1');   // 문자형 키
map.set(1, 'num1');     // 숫자형 키
map.set(true, 'bool1'); // 불린형 키

// 키를 문자형으로 변환하는 객체와는 달리 map은 키 타입을 변환시키지 않고 그대로 유지
alert( map.get(1)   ); // 'num1'
alert( map.get('1') ); // 'str1'

alert( map.size ); // 3
```

**맵은 키로 객체를 허용합니다.**

```jsx
let john = { name: "John" };
let visitsCountMap = new Map();

visitsCountMap.set(john, 123);

alert( visitsCountMap.get(john) ); // 123
```

객체를 키로 사용할 수 있다는 점은 `맵`의 가장 중요한 기능 중 하나입니다. `객체`에는 문자열 키를 사용할 수 있습니다. 하지만 객체 키는 사용할 수 없습니다. 

**맵 체이닝**

```jsx
map.set('1', 'str1')
  .set(1, 'num1')
  .set(true, 'bool1');
```

### 맵의 요소에 반복 작업하기

아래의 세 메서드를 사용하여 `맵`의 각 요소에 반복 작업을 할 수 있습니다. 

- `map.keys()` - 각 요소의 키를 모은 반복 가능한(이터러블) 객체를 반환합니다.
- `map.values()` - 각 요소의 값을 모은 이터러블 객체를 반환합니다.
- `map.entries()` - 요소의 `[키, 값]`을 한 쌍으로 하는 이터러블 객체를 반환합니다.

```jsx
let recipeMap = new Map([
  ['cucumber', 500],
  ['tomatoes', 350],
  ['onion',    50]
]);

// 키(vegetable)를 대상으로 순회합니다.
for (let vegetable of recipeMap.keys()) {
  alert(vegetable); // cucumber, tomatoes, onion
}

// 값(amount)을 대상으로 순회합니다.
for (let amount of recipeMap.values()) {
  alert(amount); // 500, 350, 50
}

// [키, 값] 쌍을 대상으로 순회합니다.
for (let entry of recipeMap) { // recipeMap.entries()와 동일합니다.
  alert(entry); // cucumber,500 ...
}
```

추가로 `맵`은 `배열`과 유사하게 내장 메서드 `forEach`도 지원합니다. 

```jsx
// 각 (키, 값) 쌍을 대상으로 함수를 실행
recipeMap.forEach( (value, key, map) => {
  alert(`${key}: ${value}`); // cucumber: 500 ...
});
```

### 객체를 맵으로 바꾸기 : Object.entries

각 요소가 키-값 쌍인 배열이나 이터러블 객체를 초기화 용도로 `맵`에 전달해 새로운 `맵`을 만들 수 있습니다. 

```jsx
let obj = {
  name: "John",
  age: 30
};

let map = new Map(Object.entries(obj));

alert( map.get('name') ); // John
```

### 맵을 객체로 바꾸기 : Object.fromEntries

```jsx
let map = new Map();
map.set('banana', 1);
map.set('orange', 2);
map.set('meat', 4);

let obj = Object.fromEntries(map.entries()); // 맵을 일반 객체로 변환 (*)

// 맵이 객체가 되었습니다!
// obj = { banana: 1, orange: 2, meat: 4 }

alert(obj.orange); // 2
```

## Set

`셋(set)`은 중복을 허용하지 않는 값을 모아 놓은 컬렉션입니다. 

셋의 주요 메서드는 아래와 같습니다. 

- `new Set(iterable)` - set을 만듭니다. `이터러블`객체를 전달 받으면 그 안의 값을 복사해 셋에 넣어줍니다.
- `set.add(value)` - 값을 추가하고 셋 자신을 반환합니다.
- `set.delete(value)` - 값을 제거합니다. 호출 시점에 Set 내에 값이 있어서 제거에 성공하면 `true` 아니면 `false`를 반환합니다.
- `set.has(value)` - 셋 내에 값이 존재하면 `true`, 아니면 `false`를 반환합니다.
- `set.clear()` - 셋을 비웁니다.
- `set.size` - 셋에 몇 개의 값이 있는지 세줍니다.

Set 내에 동일한 값(value)가 있다면 `set.add(value)`를 아무리 많이 호출하더라도 아무런 반응이 없습니다. 

```jsx
let set = new Set();

let john = { name: "John" };
let pete = { name: "Pete" };
let mary = { name: "Mary" };

// 어떤 고객(john, mary)은 여러 번 방문할 수 있습니다.
set.add(john);
set.add(pete);
set.add(mary);
set.add(john);
set.add(mary);

// 셋에는 유일무이한 값만 저장됩니다.
alert( set.size ); // 3

for (let user of set) {
  alert(user.name); // // John, Pete, Mary 순으로 출력됩니다.
}
```

### Set 값에 반복 작업하기

`for..of`나 `forEach`를 사용하면 set 값을 대상으로 반복 작업을 수행할 수 있습니다. 

```jsx
let set = new Set(["oranges", "apples", "bananas"]);

for (let value of set) alert(value);

// forEach를 사용해도 동일하게 동작합니다.
set.forEach((value, valueAgain, set) => {
  alert(value);
});

// 위와 동일하게 동작
set.forEach((value, set) => {
  alert(value);
});
```

두 번째 인수에 *같은 값*인 `valueAgain`을 받는데 이는 `맵`과의 호환성 때문입니다. 생략해도 정상적으로 동작합니다.
