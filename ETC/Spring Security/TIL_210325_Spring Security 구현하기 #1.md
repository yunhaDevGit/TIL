# Spring Security 구현하기 #1



```sql
create user 'security'@'%' identified by 'security';
grant all privileges on *.* to 'security'@'%';
create database security;
use security;
```

<img src="https://lh4.googleusercontent.com/hfma_QCjBRSwPHVyUxO1lTw77YMm6h2KKVt3ub0VEXOzUOUhaybK8zn_iJ9x7sAq71NzGWM5XHVJ8-xJVCxOSYfUFHQCZP1S5tYGHJfmplKOn_RM4wRsJaD5acV9VAzX9po_WawA" alt="img" style="zoom:67%;" />

Spring Boot DevTools : 핫 리로딩(소스파일 수정 후 저장하면 자동으로 리로딩 된다)

Lombok : Getter, Setter, 생성자, Builder 패턴 사용하기 위해서

Spring Data JPA : ORM 사용하기 위해서 쓴다

MySQL Driver

Spring Security

Mustache : 템플릿 엔진(spring에서 사용하라고 권장하는 템플릿 엔진이다)

Spring Web : 웹과 관련된 어노테이션을 사용하기 위해



> 머스테치 기본 폴더 : /src/main/resources/

> view resolver 설정 : templates (prefix), mustache(suffix) 



**application.yml**

```yml
server:
  port: 8080
  servlet:
    context-path: /
    encoding:
      charset: UTF-8
      enabled: true
      force: true

spring:
  datasource:
    driver-class-name: org.mariadb.jdbc.Driver
    url: jdbc:mariadb://localhost:4000/security?serverTimezone=Asia/Seoul
    username: security
    password: security

#  pom.xml에 mustache 의존성을 추가해줬기 때문에 자동으로 기본 경로가 잡히고
#  뷰 리졸버 경로도 기본으로 등록되기 때문에 생략 가능
#  mvc:
#    view:
#      prefix: /templates/
#      suffix: .mustache

  jpa:
    hibernate:
      ddl-auto: create #create update none
      naming:
        physical-strategy: org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
    show-sql: true

```

**index.html**

```html
<!DOCTYPE html>
<html>
<head>
 <meta charset="UTF-8">
 <title>index page</title>
</head>
<body>
<h1>인덱스 페이지입니다</h1>
</body>
</html>
```

 **IndexController.java**

```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller // view return
public class IndexController {
 // mustache 기본 폴더 src/main/resources/
 // view resolver : templates (prefix), .mustache (suffix) 생략가능
 @GetMapping({"", "/"})
 public String index(){
   return "index"; // src/main/resources/templates/index.mustache
 }

}
```



**WebMvcConfig.java**

```java
import org.springframework.boot.web.servlet.view.MustacheViewResolver;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ViewResolverRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebMvcConfig implements WebMvcConfigurer {

 // view resolver를 재설정
 @Override
 public void configureViewResolvers(ViewResolverRegistry registry) {
   MustacheViewResolver resolver = new MustacheViewResolver();
   resolver.setCharset("UTF-8");
   resolver.setContentType("text/html;charset=UTF-8"); // 던지는 data는 html이고, utf-8이다
   resolver.setPrefix("classpath:/templates/"); // classpath=프로젝트 경로
   resolver.setSuffix(".html"); // .html 파일을 만들어도 mustache가 인식을 한다.

   registry.viewResolver(resolver); // registry로 view resolver 등록
 }
}
```



spring boot 실행

![img](https://lh4.googleusercontent.com/9Msxki5qnFA-ezSFCroxDhKdGvvH0DFosHF4Pj2sXYQgPbEZxGIj0GDmBN0P-08RCq6HT73SDTlqtmOWHoWs46JHPzxjGGTe6bBSU8omdJSzSMKqIQRai3IzDv0u-tOgktvijjRf)

기본적으로 security 의존성을 설정하게 되면 홈페이지로 들어가는 모든 주소가 막혀서 인증이 필요한 홈페이지가 된다. 

최초 id : user

최초 password : spring boot 실행 시 콘솔에 나오는 password 복사