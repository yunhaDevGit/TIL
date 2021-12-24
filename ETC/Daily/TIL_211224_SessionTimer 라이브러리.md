### React Idle Timer
- React Library
https://www.npmjs.com/package/react-idle-timer

### Idle Timeout이란?
Idle Timeout은 사용자 또는 클라이언트가 Web Application에서 비활성 상태로 유지되는 시간입니다. 

이는 사용자가 애플리케이션에 로그인했지만 특정 시간 동안 화면에서 아무 작업도 수행하지 않았음을 의미합니다.  

일반적으로 개인정보가 많은 고위험 Web Application에서의 Idle Timeout 시간은 2-5분이며, 저위험 Web Application에서의 Idle Timeout 시간은 15-30분입니다. 



### 사용 이유?
보안상의 이유로 API에 대한 불필요한 호출을 피하기 위해 Idle Timer가 필요합니다. 

React Idle Timer를 사용하지 않을 경우,  backend API를 매 초 또는 30초마다 하게 됩니다.  

React Idle Timer를 사용할 경우불필요한 API call을 피할 수 있습니다. 

물론 기존의 방식처럼 API call을 통한 session timer 기능을 구현할 수 있지만 React Idle Timer를 사용할 경우 쉽고 간단하게 구현할 수 있을 뿐만 아니라 다양한 옵션도 제공합니다. 


### Idle Timer에서 제공하는 옵션
- mousemove
- keydownwheel
- DOMMouseScroll
- mousewheel
- mousedown
- touchstart
- touchmove
- MSPointerDown
- MSPointerMove
- visibilitychange


### 속성
- Timeout
- Events
- onIdle
- onActive
- onAction
- Element
- StartOnMount
- StopOneIde


### Method
- reset()
- pause() 
- resume()
- getRemainingTime() 
- getElapsedTime()
- getLastActiveTime()
- isIdle()


### 라이센스
MIT 라이센스



### 사용법
설치
```
yarn add react-idle-timer
or
npm install react-idle-timer --save
```

import
```
import IdleTimer, {useIdleTimer} from ‘react-idle-timer’
```

