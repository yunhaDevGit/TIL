# Whitelabel Error Page

Spring Boot project를 아래와 같이 구현하였다.

##### Controller.java

![img](https://lh4.googleusercontent.com/1wvAJzPmE2x_eQOrPJUGkTQun-CkFu3ZzraXe56rryoK-dtYUpTq8XgVLzEsDIFXsRbUlmnyJFjrtP8UaS6cFfhJsS11u7NMq0VBnY5eSaf1ZFgQs0pB7t6-eZwZ-GEahNMsjeU5)

 

##### WebMvcConfigurer.java

![img](https://lh3.googleusercontent.com/4Qja7e6wW7KDGncwj6LETzlvaV9AskDnbaHVXeC-1sPRLPnrDZilpGSqcS27iQ-UJZeqPjTSM0eaIjcNTiArOuIvfLsdO50vBIG3jClL9JqhEh-YEzOCCN2sQdpySicLW1FqWDDf)



##### test.html

![img](https://lh4.googleusercontent.com/424JrFhBIX7Gd4l1Bt5h1yk5hBljh5bCA1l9wek9ghk0_sBWcQA1--IisJsucTwHveFzA_bHenrb12qznbIuBKZEfAAMBPhM0_e2UONUPxUaN2k6Lj4LOaOTPQ6lHmQb_mHsuTWE)





실행하여 localhost:8080/test로 접속 시도를 했을 경우, 다음과 같은 에러 페이지가 나왔다

![img](https://lh3.googleusercontent.com/FmK0yvAhcKhZaAiRzH0tjAS40k1c85X7FBXUDrgZx3Zt3OAaPDDR5mZktm_p-IDOOHV10wUBmI8CeWv9ABeEWYANqqgWQeER5ajRCsTYOX2NUWKIKqdeAMPHeYQYyfhhrq4sLCnC)



처음에는 Check your ViewResolver setup이라는 메세지를 보고 WebMvcConfigurer에 설정해주었는데 왜 그러지?라는 생각을 했다.


#### 방법 1

해당 메세지에 대해 찾아보니 build.gradle에 thymeleaf 종속성을 추가해주면 된다 하여 build.gradle에 다음과 같이 종속성을 추가해줬다

```
implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
```

해당 종속성 추가 후 다시 실행 시켰더니

![img](https://lh6.googleusercontent.com/CVmGsaWiyasF8ScAtV-zJtuJ0ijln02Ul59Sqevoc_eWyH_Zsb4qvkA1n0s1ZzbOz5Kv6l0lDfc2lJkcacP0xQnWj_Nt4DgHKPGMeEKvds6HHrUjve6QhCQEkgfccgjAB3595XMh)

다음과 같이 페이지가 제대로 잘 보였다.



spring-starter-web 종속성만 추가했을 경우, localhost:8080/test.html과 같이 전체 이름으로 src/main/resources/static 내에 있는 html 파일을 제공할 수 있다.

하지만, spring-boot-starter-thymeleaf를 추가하면 자동으로 html 파일에 매핑된다.

thymeleaf에 대한 내용을 잘 모르므로 추후에 좀 더 정리해보자.


#### 방법 2
만약 Thymeleaf 종속성을 추가하지 않고 다른 방법을 사용하고 싶다면 간단하다.
그냥 return 할 때, 'xx.html' 이런식으로 리턴을 해주면 된다. 
Tymeleaf가 /src/main/resources/templates 안에 있는 html 파일에 자동으로 매핑을 해주는 템플릿 엔진인데, 
사용하지 않는다면, 그냥 직접 매핑 시켜주면 되는 것 아닌가?

아까 추가해준 종속성을 지우고, 아래와 같이 리턴할 때 확장자까지 써줬더니 원하는대로 잘 동작하였다.
```java
  @GetMapping("/test")
  public String test() {
    return "test.html";
  }
```

#### 방법3
방법2와 유사한데, Controller는 처음처럼 return "test"를 해주고, WebMvcConfig의 addViewControllers를 다음과 같이 수정하면 정상적으로 잘 동작하는 것을 볼 수 있다
![img](https://user-images.githubusercontent.com/48197531/117766375-71c5d480-b26a-11eb-82c3-ad3b971c96da.png)

addViewController는 특정 GET 요청에 대해 추가 작업 없이 뷰만 리턴해주면 되는 경우, 해당 요청에 대한 뷰를 등록해주면 된다
즉, FreeBoardController에 @GetMapping("/test") 메서드를 따로 작성해주지 않아도 된다.


### 유의할 점!
방법1처럼 Thymeleaf라는 템플릿 엔진을 통해 html과 자동매핑 된다면, 해당 html은 /src/main/resources/template에 있어야 한다!!

반대로, 방법2와 방법3은 html 파일이 /src/main/resources/static에 있어야 한다