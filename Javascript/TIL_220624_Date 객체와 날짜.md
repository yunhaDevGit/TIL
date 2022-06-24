# Date 객체와 날짜

### 객체 생성

`new Date()`를 호출하면 새로운 `Date` 객체가 만들어지는데, `new Date()`는 다음과 같은 형태로 호출할 수 있습니다. 

**`new Date()`**

인수 없이 호출하면 현재 날짜와 시간이 저장된 `Date` 객체가 반환 됩니다. 

```jsx
let now = new Date();
alert(now); // 현재 날짜 및 시간이 출력됨 
// Thu Jun 09 2022 14:22:33 GMT+0900 (Korean Standard Time)
```

**`new Date(milliseconds)`**

UTC 기준 1970년 1월 1일 0시 0분 0초에서 `milliseconds` 후의 시점이 저장된 `Date`가 반환 됩니다.

```jsx
// 1970년 1월 1일 0시 0분 0초(UTC+0)를 나타내는 객체
let Jan01_1970 = new Date(0);
alert( Jan01_1970 );
```

1970년 첫날을 기준으로 흘러간 밀리초를 나타내는 정수는 *타임 스탬프(timestamp)*라고 부릅니다. 

타임 스탬프를 사용하면 날짜를 숫자 형태로 간편하게 나타낼 수 있습니다. `new Date(timestamp)`를 사용해 특정 날짜가 저장된 `Date` 객체를 손쉽게 만들 수 있고 `date.getTime()`을 사용해 `Date` 객체에서 타임 스탬프를 추출할 수 있습니다.

**`new Date(datestring)`**

인수가 하나인데, 문자열일 경우 문자열은 자동으로 구문 분석됩니다. 구문 분석에 적용되는 알고리즘은 `Date.parse`에서 사용하는 알고리즘과 동일합니다. 

```jsx
let date = new Date("2017-01-26");
alert(date);
// 인수로 시간은 지정하지 않았기 때문에 GMT 자정이라고 가정하고
// 코드가 실행되는 시간대(timezone)에 따라 출력 문자열이 바뀝니다.
// 따라서 얼럿 창엔
// Thu Jan 26 2017 11:00:00 GMT+1100 (Australian Eastern Daylight Time)
// 혹은
// Wed Jan 25 2017 16:00:00 GMT-0800 (Pacific Standard Time)등이 출력됩니다.
```

**`new Date(year, month, date, hours, minutes, seconds, ms)`**

주어진 인수를 조합해 만들 수 있는 날짜가 저장된 객체가 반환 됩니다. 

첫 번째와 두 번째 인수만 필수값입니다. 

- `year`는 반드시 네 자리 숫자여야 합니다.
- `month`는 `0`(1월)부터 `11`(12월) 사이의 숫자여야 합니다.
- `date`는 일을 나타내는데, 값이 없는 경우 1일로 처리됩니다.
- `hours/minutes/seconds/ms`에 값이 없는 경우 `0`으로 처리됩니다.

```jsx
new Date(2011, 0, 1, 0, 0, 0, 0); // 2011년 1월 1일, 00시 00분 00초
new Date(2011, 0, 1); // hours를 비롯한 인수는 기본값이 0이므로 위와 동일
```

### 날짜 구성 요소 얻기

`Date`객체의 메서드를 사용하여 연, 월, 일 등 값을 얻을 수 있습니다. 

getFullYear()

- 연도(네 자릿수)를 반환합니다.

getMonth()

- 월을 반환합니다(**0 이상 11 이하**).

getDate()

- 일을 반환합니다(1 이상 31 이하). 어! 그런데 메서드 이름이 뭔가 이상하네요.

getHours(), getMinutes(), getSeconds(), getMilliseconds()

- 시, 분, 초, 밀리초를 반환합니다.

getDay()

- 일요일(`0`)부터 토요일(`6`)까지 숫자 중 하나를 반환합니다.

getTime()

- 주어진 일시와 1970년 1월 1일 00시 00분 00초 사이의 간격인 타임 스탬프를 반환합니다.

getTimezoneOffset()

- 현지 시간과 표준 시간의 차이를 반환합니다.

### 날짜 구성 요소 설정하기

- `setFullYear(year, [month], [date])`
- `setMonth(month, [date])`
- `setDate(date)`
- `setHours(hour, [min], [sec], [ms])`
- `setMinutes(min, [sec], [ms])`
- `setSeconds(sec, [ms])`
- `setMilliseconds(ms)`
- `setTime(milliseconds)` (1970년 1월 1일 00:00:00 UTC부터 밀리초 이후를 나타내는 날짜를 설정)

### 자동 고침

`Date`객체엔 *자동 고침*이라는 기능이 있습니다. 

범위를 벗어나는 값을 설정하려고 하면 자동 고침 기능이 활성화되면서 값이 자동으로 수정됩니다. 

```jsx
let date = new Date(2013, 0, 32); // 2013년 1월 32일은 없습니다.
alert(date); // 2013년 2월 1일이 출력됩니다.
```

자동 고침을 통해 일정 시간이 지난 후의 날짜를 구할 수 있습니다. 

```jsx
let date = new Date();
date.setSeconds(date.getSeconds() + 70);

alert( date ); // 70초 후의 날짜가 출력됩니다.
```

0이나 음수를 사용하여 날짜 구성 요소를 설정할 수 있습니다. 

```jsx
let date = new Date(2016, 0, 2); // 2016년 1월 2일

date.setDate(1); // 1일로 변경합니다.
alert( date ); // 01 Jan 2016

date.setDate(0); // 일의 최솟값은 1이므로 0을 입력하면 전 달의 마지막 날을 설정한 것과 같은 효과를 봅니다.
alert( date ); // 31 Dec 2015
```

### Date 객체를 숫자로 변경해 시간차 측정하기

```jsx
let start = new Date(); // 측정 시작

// 원하는 작업을 수행
for (let i = 0; i < 100000; i++) {
  let doSomething = i * i * i;
}

let end = new Date(); // 측정 종료

alert( `반복문을 모두 도는데 ${end - start} 밀리초가 걸렸습니다.` );
```

### Date.now()

`Date` 객체를 만들지 않고도 시차를 측정할 수 있습니다.

현재 타임 스탬프를 반환하는 메서드 `Date.now()`를 응용하면 됩니다. 

`Date.now()`는 `new Date().getTime()`과 의미론적으로 동일하지만 중간에 `Date` 객체를 만들지 않는다는 것이 다릅니다. 그러므로 `new Date().getTime()`을 사용하는 것보다 빠르고 가비지 컬렉터의 일을 덜어줍니다. 

### Date.parse와 문자열

`Date.parse(str)`을 사용하면 문자열에서 날짜를 읽어올 수 있습니다. 

단, 문자열의 형식은 `YYYY-MM-DDTHH:mm:ss.sssZ`처럼 생겨야 합니다.

- `YYYY-MM-DD` - 날짜(연-월-일)
- `"T"` - 구분 기호로 쓰임
- `HH:mm:ss.sss` - 시:분:초.밀리초
- `'Z'`(옵션) - `+-hh:mm` 형식의 시간대를 나타냅니다. `Z` 한 글자인 경우엔 UTC+0을 나타냅니다.

`YYYY-MM-DD`, `YYYY-MM`, `YYYY`같이 더 짧은 문자열 형식도 가능합니다.

위 조건을 만족하는 문자열을 대상으로 `Date.parse(str)`을 호출하면 문자열과 대응하는 날짜의 타임 스탬프가 반환됩니다. 문자열의 형식이 조건에 맞지 않을 경우 `NaN`이 반환 됩니다.
