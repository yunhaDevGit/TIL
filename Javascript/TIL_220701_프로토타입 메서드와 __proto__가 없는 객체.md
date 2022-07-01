# ****프로토타입 메서드와 __proto__가 없는 객체****

`__proto__`는 브라우저를 대상으로 개발할 경우 구식이기 때문에 더 사용하지 않는 것이 좋습니다. 

대신 아래와 같은 메서드를 사용하는 것이 좋습니다. 

- `Object.create(proto, [descriptors])` - `[[Prototype]]`이 `proto`를 참조하는 빈 객체를 만듭니다. 이때 프로퍼티 설명자를 추가로 넘길 수 있습니다.
- `Object.getPrototypeOf(obj)` - `obj`의 `[[Prototype]]`을 반환합니다.
- `Object.setPrototypeOf(obj, proto)` -  `obj`의 `[[Prototype]]`이 `proto`가 되도록 설정합니다.

```jsx
let animal = {
  eats: true
};

// 프로토타입이 animal인 새로운 객체를 생성합니다.
let rabbit = Object.create(animal);

alert(rabbit.eats); // true

alert(Object.getPrototypeOf(rabbit) === animal); // true

Object.setPrototypeOf(rabbit, {}); // rabbit의 프로토타입을 {}으로 바꿉니다
```

`Object.create`에 프로퍼티 설명자를 선택적으로 전달할 수 있고, 설명자를 이용해 새 객체에 프로퍼티를 추가할 수 있습니다.

```javascript
let animal = {
  eats: true
};

let rabbit = Object.create(animal, {
  jumps: {
    value: true
  }
});

alert(rabbit.jumps); // true
```
