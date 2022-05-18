# SSO와 Console 로그 아웃 동기화

sso 통합 로그인에서 로그인 후 console을 키게 되면 새로운 윈도우 창이 열리고 console 화면이 보인다. 이때, sso나 console에서 로그아웃 했을 경우 동기화가 제대로 안되는 이슈가 있었다.

해당 이슈 처리 후 새로운 이슈 발생

이슈 발생 과정

1. sso 통합 로그인
2. console 윈도우 열기
3. sso 로그 아웃
4. console에서 다른 화면 클릭 시 sso 로그인 창 뜬다
5. sso에서 재로그인 시 마지막에 콘솔에서 눌렀던 화면이 나온다



**이슈 처리 방향성**

console은 새로운 윈도우에서 뜨게 되어있고, 쿠키 처리 문제로 인해 해당 이슈 발생되는 것으로 보였다. 왜인지 모르겠으나 location의 redirectUrl이 뭔가 이상했음,,,

기존에는 sso 로그아웃 시 console에서 sso 로그인 화면이 보이게끔 되어 있었는데 해당 부분을 **sso 로그아웃 시 console 윈도우가 닫히도록 수정**





application.properties

```properties
hi.https = https
hi.url = hello.hi.co.kr
```



main.layout.html

```html
<button type="button" th:title="#{site.my.logout}" th:onclick="|javascript:logOut();|">logout
</button>  

...

<script type="text/javascript">

  function logOut() {
    // application.properties 값 읽어오는 부분 (유지보수성을 위해)
	var url='[[|${@environment.getProperty('hi.https')}://${@environment.getProperty('hi.url')}/logout|]]';
	opener.parent.location=url;
	window.opener.focus();
	window.close();
  }
</script> 
...
```



sso.layout.html

```html
...
<button type="button" class="header_util_btn_logout"
        th:if="${session.userInfo} != null and ${session.userInfo.subscribe} == true"
        th:onclick="|javascript:logOut();|">로그아웃</button>
...
<script type="text/javascript" th:inline="javascript"
            th:if="${session.userInfo} != null and ${session.userInfo.subscribe} == true">
...
	function logOut() {
		if (consolePopup != null) {
			consolePopup.close();
		}
		self.focus();
		window.location.replace("/logoutAllClient");
	}
...
```



