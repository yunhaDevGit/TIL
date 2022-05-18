# Login 시 엔터 키 입력 이벤트가 두번씩 발생하는 이슈

login.js

```javascript
var LoginUI = (function(){
    var
    modules = {},
    LoginView = Backbone.View.extend({
        el: "main",
		
        events: {
            "keyup :input": "keyUpEvent",
        },
        
        keyUpEvent: function(e){
            if(e.keyCode === 13){
                this.login();
            }
        },
        
        initialize: function() {
           ...
        },
        initLogin: function(){
           ...
        },
        login: function(){
            this.initLogin();
            ...
        }
        ...

```



login.html

```html
<div class="login_form" style="margin-top: 35px">
 <input type="text"id="loginId" name="username" tabindex="1" placeholder="아이디 입력" class="input_login">
 <input type="password" id="loginPass" name="password" tabindex="2" placeholder="비밀번호 입력" 
        class="input_login">
 <button type="button" class="login_btn" onclick="LoginUI.modules.loginView.login();return false;">
     LOGIN</button>
</div>
```



**이슈**

아이디, 비밀번호 입력 후 Enter Key 누르면 keyUpEvent함수가 두 번 호출된다.

Enter Key가 아닌 Click을 통해 로그인 시도를 하면 한번만 호출되었다.



**처리 방법 1**

```javascript
events: {
	"keyup :input": "keyUpEvent",
},
```

해당 events의 input이 뭔지 몰라 <input 태그인가 싶어 button에 id를 넣어서 id 값을 event에 지정해줬다

```html
<button type="button" id="loginButton" class="login_btn" onclick="LoginUI.modules.loginView.login();return false;">LOGIN</button>
```

```javascript
events: {
	"keyup :#loginButton": "keyUpEvent",
},
```

--> Enter Key가 아예 안먹힌다.



**처리 방법 2**

jQuery의 isImmediatePropagationStopped 활용

```javascript
keyUpEvent: function(e){
	if(e.keyCode === 13){
		this.login();
        e.isImmediatePropagationStopped()
	}
},
```

--> 역시 Enter Key가 안먹힘



**의문점**

왜 Enter Key를 누르면 keyUpEvent함수가 두번 호출이 되고, click을 하면 한번만 호출되지?



**궁금증 해결 과정**

click event와 keyup event의 다른점은 button에 직접 event를 달았는지와 event를 backbone.js에서 직접 선언하고 호출하는지의 차이

```html
<button type="button" id="loginButton" class="login_btn">LOGIN</button>
```

```javascript
var LoginUI = (function(){
    var
    modules = {},
    LoginView = Backbone.View.extend({
        el: "main",
		
        events: {
            "keyup :input": "keyUpEvent",
            "click :#loginButton": "clickEvent",
        },
        
        clickEvent: function(e){
            if(e.keyCode === 13){
                this.login();
            }
        },
        
        keyUpEvent: function(e){
            if(e.keyCode === 13){
                this.login();
            }
        },
        ...

```

다음과 같이 수정한 후, 로그인을 click으로 시도해보았다.

어랏! click으로 할 때도, clickEvent가 두번 호출된다!

해당 events 선언? 사용법이 잘 못 된거라 판단. 하지만 최대한 빨리 처리해야 하는 이슈이므로 그냥 아래와 같이 처리하였다.





**이슈 해결!! :happy:**

*login button에 id 추가해준 뒤 해당 버튼에 addEventListener를 통해 keyup event를 직접 달아주었다.*

```html
...
	<input type="text" id="loginId" name="username" placeholder="아이디 입력" class="input_login">
	<input type="password"  id="loginPass" name="password" placeholder="비밀번호 입력" class="input_login">
	<button type="button" id="loginButton" class="login_btn" 
            onclick="LoginUI.modules.loginView.login();return false;">LOGIN</button>
...
...
<div layout:fragment="script" th:remove="tag">
    <script type="text/javascript" src="/js/sso/login.js"></script>
    <script type="text/javascript">
        const button = document.getElementById('loginButton');
        document.addEventListener('keyup', keyCheck);

        function keyCheck(e) {
            if(e.keyCode === 13){
                LoginUI.modules.loginView.login();
            }
        }
    </script>
</div>
```

