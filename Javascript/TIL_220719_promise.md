# Promise

`promise` 객체는 아래와 같은 문법으로 만들 수 있습니다.

```jsx
let promise = new Promise(function (resolve, reject) {
	// executor (제작 코드, 가수)
});
```

`new Promise`에 전달되는 함수는 *executor(실행자, 실행 함수)*라고 부릅니다. executor는 `new Promise`가 만들어질 때 자동으로 실행되는데, 결과를 최종적으로 만들어내는 제작 코드를 포함합니다. 

executor의 인수 `resolve`와 `reject`는 자바스크립트에서 자체 제공하는 콜백입니다. 그러므로 두 인수를 신경 쓰지 않고 executor 안 코드만 작성하면 됩니다. 

대신 executor에선 결과를 즉시 얻든 늦게 얻든 상관없이 상황에 따라 인수로 넘겨준 콜백 중 하나를 반드시 호출해야 합니다. 

- `resolve(value)` - 일이 성공적으로 끝난 경우 그 결과를 나타내는 `value`와 함께 호출
- `reject(error)` - 에러 발생 시 여러 객체를 나타내는 `error`와 함께 호출

executor는 처리 성공 여부에 따라 `resolve`나 `reject`를 호출합니다. 

한편, `new Promise` 생성자가 반환하는 `promise`객체는 다음과 같은 내부 프로퍼티를 갖습니다. 

- `state` - 처음엔 `"pending"(보류)`이었다 `resolve`가 호출되면 `"fulfilled"`, `reject`가 호출되면 `"rejected"`로 변합니다.
- `result` - 처음엔 `undefined`이었다 `resolve(value)`가 호출되면 `value`로 `reject(error)`가 호출되면 `error`로 변합니다.

```jsx
let promise = new Promise(function(resolve, reject) {
  // 프라미스가 만들어지면 executor 함수는 자동으로 실행됩니다.

  // 1초 뒤에 일이 성공적으로 끝났다는 신호가 전달되면서 result는 '완료'가 됩니다.
  setTimeout(() => resolve("완료"), 1000);
});
```

1. executor는 `new Promise`에 의해 자동으로 그리고 즉각적으로 호출됩니다. 
2. executor는 인자로 `resolve`와 `reject` 함수를 받습니다. 이 함수들은 자바스크립트 엔진이 미리 정의한 함수이므로 개발자가 따로 만들 필요 없습니다. 

> executor는 `resolve`나 `reject` 중 하나를 반드시 호출해야 합니다.
> 

### 소비자: then, catch, finally

프라미스 객체는 **executor**와 결과나 에러를 받을 **소비 함수**를 이어주는 역할을 합니다. 소비 함수는 `.then`, `.catch`, `.finally` 메서드를 사용해 등록됩니다.

**then**

`.then`은 프라미스에서 가장 기본이 되는 메서드입니다. 

```jsx
promise.then(
	function(result) { /* 결과(result)를 다룹니다. */},
	function(error) { /* 에러(error)를 다룹니다. */},
};
```

`.then`의 첫 번째 인수는 프라미스가 이행되었을 때 실행되는 함수이고, 여기서 실행 결과를 받습니다. 

`.then`의 두 번째 인수는 프라미스가 거부되었을 때 실행되는 함수이고, 여기서 에러를 받습니다.

```jsx
let promise = new Promise(function(resolve, reject) {
	setTimeout(() => resolve("완료"), 1000);
});
// resolve 함수는 .then의 첫 번째 함수(인수)를 실행합니다. 
promise.then(
	result => alert(result), // 1초 후 "완료" 출력
	error => alert(error) // 실행되지 않음
);
```

```jsx
let promise = new Promise(function(resolve, reject) {
  setTimeout(() => reject(new Error("에러 발생!")), 1000);
});

// reject 함수는 .then의 두 번째 함수를 실행합니다.
promise.then(
  result => alert(result), // 실행되지 않음
  error => alert(error) // 1초 후 "Error: 에러 발생!"을 출력
);
```

작업이 성공적으로 처리된 경우만 다루고 싶을 경우 `.then`에 인수를 하나만 전달하면 됩니다.

```jsx
let promise = new Promise(resolve => {
  setTimeout(() => resolve("완료!"), 1000);
});

promise.then(alert); // 1초 뒤 "완료!" 출력
```

**catch**

에러가 발생한 경우만 다루고 싶다면 `.catch`를 사용해도 되는데 `.catch`는 `.then`에 `null`을 전달하는 것과 동일하게 동작합니다. 

```jsx
let promise = new Promise((resolve, reject) => {
	setTimeout(() => reject(new Error("에러 발생")), 1000)
});

promise.catch(alert); // 1초 뒤 Error: 에러 발생 출력
```

`.catch(f)`는 문법이 간결하다는 점만 빼고 `.then(null,f)`과 완벽하게 같습니다.

**finally**

`try {...} catch {...}`에 finally 절이 있는 것처럼, 프라미스에도 `finally`가 있습니다. 

쓸모 없어진 로딩 인디케이터를 멈추는 경우 같이 결과가 어떻든 마무리가 필요하면 `finally`가 유용합니다. 

```jsx
new Promise((resolve, reject) => {
	/* 시간이 걸리는 어떤 일을 수행하고, 그 후 resolve, reject를 호출함 */
})
  // 성공·실패 여부와 상관없이 프라미스가 처리되면 실행됨
  .finally(() => 로딩 인디케이터 중지)
  .then(result => result와 err 보여줌 => error 보여줌)
```

1. `finally` 핸들러엔 인수가 없습니다. `finally`에선 프라미스가 이행되었는지, 거부 되었는지 알 수 없습니다. `finally`에선 절차를 마무리하는 ‘보편적’ 동작을 수행하기 때문에 성공, 실패 여부를 몰라도 됩니다. 
2. `finally` 핸들러는 자동으로 다음 핸들러에 결과와 에러를 전달합니다. 
    
    result가 `finally`를 거쳐 `then`까지 전달되는 것을 확인해봅시다. 
    
    ```jsx
    new Promise((resolve, reject) => {
    	setTimeout(() => resolve("결과"), 2000)
    })
    	.finally(() => alert("프라미스가 준비되었습니다"))
    	.then(result => alert(result)); // <-- .then에서 result를 다룰 수 있음
    ```
    
    프라미스에서 에러가 발생하고 이 에러가 `finally`를 거쳐 `catch`까지 전달되는 것을 확인해봅시다.
    
    ```jsx
    new Promise((resolve, reject) => {
      throw new Error("에러 발생!");
    })
      .finally(() => alert("프라미스가 준비되었습니다."))
      .catch(err => alert(err)); // <-- .catch에서 에러 객체를 다룰 수 있음
    ```
    
3. `.finally(f)`는 함수 `f`를 중복해서 쓸 필요가 없기 때문에 `.then(f, f)`보다 문법 측면에서 더 편리합니다. 

바로 전에 살펴보았던 스크립트 로딩에 사용되는 `loadScript`를 콜백 함수 대신 프라미스를 이용해 작성해보겠습니다.

기존에 작성 했던 콜백 함수입니다. 

```jsx
function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;

  script.onload = () => callback(null, script);
  script.onerror = () => callback(new Error(`${src}를 불러오는 도중에 에러가 발생함`));

  document.head.append(script);
}
```

프라미스를 이용해 작성한 함수입니다. 

```jsx
function  loadScript(src) {
	return new Promise(function (resolve, reject) {
		let script = document.createElement('script');
		script.src = src;

		script.onload = () => resolve(script);
		script.onerror = () => reject(new Error(`${src}를 불러오는 도중에 에러가 발생함`));

    document.head.append(script);
  });
}
```

```jsx
let promise = laodScript("https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.11/lodash.js");

promise.then(
	script => alert(`${script.src}을 불러왔습니다`);
	error => alert(`Error: ${error.message}`);
);

promise.then(script => alert('또다른 핸들러...'));
```

### Promise 체이닝

프라미스 체이닝은 아래와 같습니다.

```jsx
new Promise(function(resolve, reject) {

  setTimeout(() => resolve(1), 1000); // (*)

}).then(function(result) { // (**)

  alert(result); // 1
  return result * 2;

}).then(function(result) { // (***)

  alert(result); // 2
  return result * 2;

}).then(function(result) {

  alert(result); // 4
  return result * 2;

});
```

1. 1초 후 최초 프라미스가 이행됩니다. (*)
2. 이후 첫번째 `.then` 핸들러가 호출됩니다. (**)
3. 2에서 반환한 값은 다음 `.then` 핸들러에 전달됩니다. (***)
4. 위의 과정이 계속 이어집니다. 

### fetch와 체이닝 응용

네트워크 요청 시 promise를 자주 이용합니다.

예시에선 메서드 `fetch`를 사용해 원격 서버에서 사용자 정보를 가지고 옵니다. 

```jsx
let promise = fetch(url);
```

위 코드를 실행하면 `url`에 네트워크 요청을 보내고 프라미스를 반환합니다. 원격 서버가 헤더와 함께 응답을 보내면, 프라미스는 `response` 객체와 함께 이행됩니다. 그런데 이때 *response 전체가 오나전히 다운로드 되기 전* 프라미스는 이행 상태가 되어버립니다. 

응답이 완전히 종료 후 응답 전체를 읽으려면 `response.text()`를 호출해야 합니다. 

`response.text()`는 원격 서버에서 전송한 텍스트가 전부 다운로드 되면, 이 텍스트를 `result` 값으로 갖는 이행된 프라미스를 반환합니다. 

```jsx
fetch('/article/promise-chaining/user.json')
  // 원격 서버가 응답하면 .then 아래 코드가 실행됩니다.
  .then(function(response) {
    // response.text()는 응답 텍스트 전체가 다운로드되면
    // 응답 텍스트를 새로운 이행 프라미스를 만들고, 이를 반환합니다.
    return response.text();
  })
  .then(function(text) {
    // 원격에서 받아온 파일의 내용
    alert(text); // {"name": "Violet-Bora-Lee", "isAdmin": true}
  });

// 위 코드와 동일한 기능을 하지만, response.json()은 원격 서버에서 불러온 내용을 JSON으로 변경해줍니다.
fetch('/article/promise-chaining/user.json')
  .then(response => response.json())
  .then(user => alert(user.name)); // Violet-Bora-Lee, 이름만 성공적으로 가져옴
```

### promise 에러 핸들링

promise가 거부되면 제어 흐름이 제일 가까운 rejection 핸들러로 넘어가기 때문에 promise 페인을 이용하여 에러를 쉽게 처리할 수 있습니다. 

존재하지 않는 주소를 `fetch`에 넘겨주는 예시를 봅시다. `.catch`에서 에러를 처리합니다. 

```jsx
fetch('https://no-such-server.blbla')
	.then(response => response.json())
	.catch(err => alert(err))
```

`.catch`는 첫 번째 핸들러일 필요 없이 하나 혹은 여러 개의 `.then` 뒤에 올 수 있습니다. 

### 암시적 try…catch

executor와 promise 핸들러 코드 주위엔 ‘암시적 `try..catch`가 있습니다. 예외가 발생하면 암시적 `try...catch`에서 예외를 잡고 이를 reject 처럼 다룹니다. 

```jsx
new Promise((resolve, reject) => {
	throw new Error("에러 발생");
}).catch(alert); // Error: 에러 발생
```

아래도 동일하게 동작합니다.

```jsx
new Promise((resolve, reject) => {
	reject(new Error("에러 발생"));
}).catch(alert); // Error: 에러 발생
```

executor 주위의 ‘암시적 `try...catch`'는 스스로 에러를 잡고, 에러를 거부 상태의 promise로 변경시킵니다. 

이는 executor 뿐만 아니라 핸들러에서도 발생합니다. 

```jsx
new Promise((resolve, reject) => {
  resolve("OK");
}).then((result) => {
  throw new Error("에러 발생!"); // 프라미스가 거부됨
}).catch(alert); // Error: 에러 발생!

// ---------
new Promise((resolve, reject) => {
  resolve("OK");
}).then((result) => {
  blabla(); // 존재하지 않는 함수
}).catch(alert); // ReferenceError: blabla is not defined
```

### Promise API

`Promise`에는 5가지 정적 메서드가 있습니다. 

1. `Promise.all(promises)` - 모든 프라미스가 이행될 때까지 기다렸다가 그 결괏값을 담은 배열을 반환합니다. 주어진 프라미스 중 하나라도 실패하면 `Promise.all`은 거부되고, 나머지 프라미스의 결과는 무시됩니다. 
2. `Promise.allSettles(promises)` - 최근에 추가된 메서드로 모든 프라미스가 처리될 때까지 기다렸다가 그 결과(객체)를 담은 배열을 반환합니다. 객체엔 다음과 같은 정보가 담깁니다.
    - `status` : `"fulfilled"` 또는 `"rejected"`
    - `value`(프라미스가 성공한 경우) 또는 `reason`(프라미스가 실패한 경우)
3. `Promise.race(promises)` - 가장 먼저 처리된 프라미스 결과 또는 에러를 담은 프라미스를 반환합니다.
4. `Promise.resolve(value)` - 주어진 값을 사용해 이행 상태의 프라미스를 만듭니다. 
5. `Promise.reject(error)` - 주어진 에러를 사용해 거부 상태의 프라미스를 만듭니다.
