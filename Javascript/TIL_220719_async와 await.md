# async와 await

`async`와 `await`를 통해 프라미스를 좀 더 편하게 사용할 수 있습니다.

### async 함수

`async`는 function 앞에 위치합니다.

```jsx
async function f() {
	return 1;
}
```

function 앞에 `async`를 붙이면 해당 함수는 항상 프라미스를 반환합니다. 프라미스가 아닌 값을 반환하더라도 이행 상태의 프라미스(resolved promise)로 값을 감싸 이행된 프라미스가 반환되도록 합니다.

`result`가 `1`인 이행 프라미스가 반환됩니다.

```jsx
async function f() {
	return 1;
}
f().then(alert); // 1
```

명시적으로 프라미스를 반환할 수 있지만 결과는 동일합니다.

```jsx
async function f() {
  return Promise.resolve(1);
}

f().then(alert); // 1
```

`async`가 붙은 함수는 반드시 프라미스를 반환하고, 프라미스가 아닌 것은 프라미스로 감싸 반환합니다.

또 다른 키워드 `await`는 `async` 함수 안에서만 동작합니다.

### await

`await` 문법은 다음과 같습니다.

```jsx
let value = await promise;
```

자바스크립트는 `await` 키워드를 만나면 프라미스가 처리될 때까지 기다립니다. 결과는 그 이후 반환됩니다.

```jsx
async function f() {
	let promise = new Promise((resolve, reject) => {
		setTimeout(() => resolve("완료"), 1000)
	});
	let result = await promise; // promise가 이행될 때까지 기다림

	alert(result); // 완료
}
 f();
```

함수 호출 후 함수 본문이 실행되는 중 await 줄에서 실행이 잠시 중단 되었다가 프로미스가 처리되면 실행이 재개됩니다.

`await`는 말 그대로 프라미스가 처리될 때까지 함수 실행을 기다리게 합니다. 프라미스가 처리되면 그 결과와 함께 실행이 재개됩니다.

`await`는 `promise.then`보다 좀 더 세련되게 프라미스의 `result` 값을 얻을 수 있습니다.

> `async` 함수가 아닌데 `await`를 사용하면 문법 에러가 발생합니다.
>

**async 클래스 메서드**

메서드 이름 앞에 `async`를 추가하면 async 클래스 메서드를 선언할 수 있습니다.

```jsx
class Waiter {
  async wait() {
    return await Promise.resolve(1);
  }
}

new Waiter()
  .wait()
  .then(alert); // 1
```

### 에러 핸들링

프라미스가 정상적으로 이행되면 `await promise`는 프라미스 객체의 `result`에 저장된 값을 반환합니다. 프라미스가 거부되면 마치 `throw`문을 작성한 것처럼 에러가 던져집니다.

```jsx
async function f() {
	await Promise.reject(new Error("에러 발생");
}
```

아래도 위와 동일합니다.

```jsx
async function f() {
  throw new Error("에러 발생!");
}
```

`await`가 던진 에러는 `throw`가 던진 에러를 잡을 때처럼 `try...catch`를 사용해 잡을 수 있습니다.

```jsx
async function f() {

  try {
    let response = await fetch('http://유효하지-않은-주소');
  } catch(err) {
    alert(err); // TypeError: failed to fetch
  }
}

f();
```

`try..catch`가 없으면 아래 예시의 async 함수 `f()`를 호출해 만든 프라미스가 거부 상태가 됩니다. `f()`에 `.catch`를 추가하면 거부된 프라미스를 처리할 수 있습니다.