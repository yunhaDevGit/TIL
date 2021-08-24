
API를 개발하면서 request body로 넘어온 도메인 객체 정보가 유효한 정보인지에 대한 validation check하는 로직을 개발하게 되었다. 
validation check를 하기 위해 Spring Framework Validator API를 사용하였다. 
아래와 같이 API를 통해 요청을 할 경우, 넘어온 파라미터 정보가 유효 한지에 대한 유효성 검사를 했다.
~~(회사 코드를 첨부할 수 없어 임의로 작성한 예시이다..)~~

```java
@RequestMapping(value = "/{path}/user", method = RequestMethod.POST)  
@ResponseStatus(value = HttpStatus.ACCEPTED)  
public UserInfo createUser(@PathVariable String path,  
		  BindingResult result,  
		  @RequestBody UserInfo userInfo,  
		  HttpServletRequest request) throws Exception {  
  
  ...  
  validatorOnUserCreate.validate(userInfo, result);  
  ...
 
  if (result.hasErrors()) {  
    throw new ValidationFailException(result);  
  }  
  
  return userService.createUser(userInfo);  
}
```

```
http://localhost:9090/test/user
```
```json
{
	"id" : "testUser",
	"name" : "유저이름",
	"password" : "password!1",
	"email" : "abc@gmail.com"
}
```

위와 같이 UserInfo를 Request Parameter로 넘겨 요청을 하였는데, Controller를 타기도 전에 아래와 같은 Exception이 발생했다.

```json
{
	"timestamp": 1629770170180,
	"status": 500,
	"error": "Internal Server Error",
	"exception": "java.lang.IllegalStateException",
	"message": "An Errors/BindingResult argument is expected to be declared immediately after the model 	attribute, the @RequestBody or the @RequestPart arguments to which they apply: public com.model.UserInfo com.api.controller.UserController.createUser(java.lang.String,org.springframework.validation.BindingResult,com.model.UserInfo,javax.servlet.http.HttpServletRequest) throws java.lang.Exception",
	"path": "/test/user"
}
```

message에도 나왔듯이 Error는 BindingResult나 Errors로 받을 수 있지만, 반드시 Model Attribute 뒤에 파라미터로 있어야 컨트롤러 단을 우선적으로 탄다고 한다. 

```java
```java
@RequestMapping(value = "/{path}/user", method = RequestMethod.POST)  
@ResponseStatus(value = HttpStatus.ACCEPTED)  
public UserInfo createUser(@PathVariable String path,  
		  @RequestBody UserInfo userInfo,  
		  BindingResult result, 
		  HttpServletRequest request) throws Exception {  
		  
		  ...
		  
}
```

위와 같이 BindingResult를 Model Attribute 뒤에 써주었더니 제대로 동작하였다!
