# @RequestParam @PathVariable
RequestParam, PathVariable 모두 **데이터 전달을 위해** 사용된다. 

## @RequestParam

- 요청 파라미터를 메소드 파라미터에 넣어주는 어노테이션이다.
- 쿼리 스트링에서 값을 가져온다.

```java
// http://localhost:8080/foos?id={id}
@GetMapping("/foos")
@ResponseBody
public String getFooById(@RequestParam String id) {
	return "ID: " + id;
}
```

- 해당 어노테이션에 명시된 매개변수 값은 반드시 파라미터 값이 넘어와야 한다. (넘어오지 않으면 400에러가 발생한다)
- 필수 파라미터가 아닌 경우 required=false 설정을 추가한다. 이렇게 하면 파라미터가 넘어오지 않아도 에러가 발생하지 않는다.
    
    ```java
    @GetMapping("/foos")
    @ResponseBody
    public String getFooById(@RequestParam(required=false) String id) {
    	return "ID: " + id;
    }
    ```
    

## @PathVariable

- URL에서의 {}에 명시된 변수를 받아온다. 즉, URI 경로의 일부를 파라미터로 사용한다.
- 템플릿 변수의 값을 추출하고 그 값을 메소드 변수에 할당하는데 사용된다.

```java
// http://localhost:8080/foos/{id}
@GetMapping("/foos/{id}")
@ResponseBody
public String getFooById(@PathVariable String id) {
    return "ID: " + id;
}
```

- URI 템플릿 변수 이름과 메소드 매개변수 이름이 반드시 동일해야 하는 것은 아니다.
    
    ```java
    @GetMapping("/foos/{id}")
    @ResponseBody
    public String getFooById(@PathVariable("id") String fooId) {
        return "ID: " + fooId;
    }
    ```
    
- 여러개의 @PathVariable 어노테이션을 사용할 시 URI 템플릿 변수 역시 어노테이션으로 지정된 매개변수 숫자에 맞게 설정되어야 한다.

## @RequestBody

- JSON 데이터를 원하는 타입의 객체로 변환해야 하는 경우 사용
- HTTP 요청의 몸체를 자바 객체로 받을 수 있게 해준다
- 비동기 처리 구현 시 @ResponseBody와 함께 자주 사용된다.
