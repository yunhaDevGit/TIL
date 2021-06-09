# html에서 enum 클래스의 값들 가지고 오기...

toy project에 사용자의 role을 일반 사용자, 매니저, 관리자 이렇게 세 가지로 만든 후 각각 role마다 접근할 수 있는 권한을 부여하였다. 
우선 화면에서 간단하게 회원 가입 할 때, role을 선택하는 방법으로 구현을 하였다.
(물론 계속 이렇게 하진 않을거다..ㅎ 따로 권한 부여하는 페이지를 만든 후 그 권한 설정하는 페이지는 관리자만이 접근할 수 있도록 나중에 수정할거다.)
하여튼 html에서 회원 가입할 때 선택할 수 있도록 구현해보았다.

#### enum class
```java
package com.toyproject.noticeboard.utils;  
public enum Role {  
  ROLE_ADMIN, ROLE_MANAGER, ROLE_USER  
}
```
#### 회원가입
```html
<!DOCTYPE html>  
<html>  
<head>  
 <meta charset="UTF-8">  
 <title>join page</title>  
</head>  
<body>  
<h1>회원가입 페이지입니다</h1>  
<form action="/join" method="POST">  
 <input type="text" name="username" placeholder="Username"/></br>  
 <input type="text" name="id" placeholder="Id"/></br>  
 <input type="password" name="password" placeholder="Password"/></br>  
 <input type="email" name="email" placeholder="email@example.com"/></br>  
 <input type="text" name="contact" placeholder="010-xxxx-xxxx"/></br>  
  
 <label><input type="radio" name="role" value="Role.ROLE_USER}">사용자</label>  
 <label><input type="radio" name="role" value="Role.ROLE_MANAGER}">매니저</label>  
 <label><input type="radio" name="role" value="Role.ROLE_ADMIN}">관리자</label><br/><br/>  
  
 <button>회원가입</button>  
</form>  
</body>  
</html>
```
html에서 Enum 클래스의 값들을 가지고 오는 방법을 몰라 우선 이렇게 실행해 보았다.
(될 거란 기대는 하지 않았지만 되길 바라는 마음으로,,,)
역시나 button을 눌러 action이 수행될 때, `failed to convert property value of type 'java.lang.string' to required type enum for property ~~` 오류가 발생했다.

html에서 enum 클래스의 값을 가지고 오는 방법을 찾았더니 thymeleaf를 사용하면 가지고 올 수 있다고 한다.
thymeleaf를 잘 몰라 그 방법 말곤 없나 싶어서 더 찾아봤지만 따로 없는 것 같다 (내가 못 찾은 거일지도...)

```html
<!DOCTYPE html>  
<html lang="en" xmlns="http://www.thymeleaf.org">  
<html>  
<head>  
 <meta charset="UTF-8">  
 <title>join page</title>  
</head>  
<body>  
<h1>회원가입 페이지입니다</h1>  
<form action="/join" method="POST">  
 <input type="text" name="username" placeholder="Username"/></br>  
 <input type="text" name="id" placeholder="Id"/></br>  
 <input type="password" name="password" placeholder="Password"/></br>  
 <input type="email" name="email" placeholder="email@example.com"/></br>  
 <input type="text" name="contact" placeholder="010-xxxx-xxxx"/></br>  
  
 <label><input type="radio" name="role" th:value="${T(com.toyproject.noticeboard.utils.Role).ROLE_USER}">사용자</label>  
 <label><input type="radio" name="role" th:value="${T(com.toyproject.noticeboard.utils.Role).ROLE_MANAGER}">매니저</label>  
 <label><input type="radio" name="role" th:value="${T(com.toyproject.noticeboard.utils.Role).ROLE_ADMIN}">관리자</label><br/><br/>  
  
 <button>회원가입</button>  
</form>  
</body>
```

결국 위와 같이 thymeleaf를 적용하여 enum 클래스의 값들을 가지고 올 수 있었다.

참고 : https://www.baeldung.com/thymeleaf-enums
