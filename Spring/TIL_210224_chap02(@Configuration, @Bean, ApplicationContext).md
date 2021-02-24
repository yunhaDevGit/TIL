spring5 프로그래밍 입문 Chap02

# @Configuration, @Bean, ApplicationContext



예제

##### Greeter.java

![img](https://lh5.googleusercontent.com/mgkXb5UFNdEBRStlK6avk9b4Hd2MgsGZ_A7JpyDZ_6GwbcKSNdnokidTcSbsPwDshA4W8DAMRdftI4ZG8MhV_uzMqcEqD1sYEF165dKIuc6TDdJ4H-e5fKm5msBf0uwGEsy4Krbd)



##### AppContext.java

![img](https://lh5.googleusercontent.com/aOypX1Of7dw8ui4rjpHp8oYba5vPbC3i6uUbgwTkhpma8cD8B9Vjwzgayv8ZV8xS59he_xlOwfa7OtzO70Zi7a3QKIvLaMUfcMpFXYW9YJaF_QFfP0gUVwO8Jf0CQdTM2bxdQ5uD)

- spring은 객체를 생성하고 초기화 하는 기능을 제공한다
- 스프링이 생성하는 객체를 빈(Bean) 객체라고 부르는데 이 빈 객체에 대한 정보를 담고 있는 메서드가 greeter() 메서드이다



- @Bean 어노테이션을 메서드에 붙이면 해당 메서드가 생성한 객체를 스프링이 관리하는 빈 객체로 등록한다

- @Bean 어노테이션을 붙인 메서드의 이름이 빈 객체의 이름이 된다. 

  (여기서는 greeter라는 이름으로 객체 구분)

  - @Bean(name="name")으로 이름 변경할 수 있다.
  - @Configuration 설정된 클래스의 메소드에서 사용할 수 있다
  - @Scope를 통해 객체 생성을 조정할 수 있다.
    - bean의 범위 설정
      - singleton - IoC 컨테이너당 하나의 빈을 리턴
      - prototype - 요구가 있을 때마다 새로운 빈을 만들어 리턴
  - @Component 어노테이션을 통해 @Configuration 없이도 빈 객체를 생성 할 수 있다.



![img](https://lh6.googleusercontent.com/FzTR5i_BvUWG0qbbi-sA54nZKa4JysF4EflvhBJQXgt26tMAtlj6znB5nJDB2mTMJEAu95dEEvPBtE9qQiGbHedZo2h1pVCMMuZknrRGnn2wVQH6Z_2B3t6aXVYiQ-TqBshHv5r_)

AnnotationConfigApplicationContext 클래스는 자바 설정에서 정보를 읽어와 빈 객체를 생성하고 관리한다. 

AnnotationConfigApplicationContext 객체를 생성할 때 앞서 작성한 AppContext 클래스를 생성자 파라미터로 전달한다

-> AnnotationConfigApplicationContext는 AppContext에 정의한 @Bean 설정 정보를 읽어와 Greeter 객체를 생성하고 초기화한다. 



getBean() 메서드는 AnnotationConfigApplicationContext가 자바 설정을 읽어와 생성한 빈 객체를 검색할 때 사용된다

getBean()메서드(빈 객체 제공)

- 첫번째 파라미터 : @Bean 어노테이션의 메서드 이름인 빈 객체의 이름
- 두번째 파라미터 : 검색할 빈 객체 타입
  - 메서드 이름은 "greeter"이고 생성한 객체의 리턴타입이 Greeter이다
  - greeter() 메서드가 생성한 Greeter 객체 리턴



실행 결과

![img](https://lh6.googleusercontent.com/68rTPhbAnoYizBeO70TO5VKH-7GA6QEIn64pfQqJKme4gVZa4bdRhmg5jL9H5Bk5UNFe3EkFiN_z-s1Goydel41beSFi_iANAZxFlQrr8hAWZA1u9iyX-HlfXY8GQeocFmtY8ioO)



##### AnnotationConfigApplicationContext가 핵심!!

스프링의 핵심 기능 : 객체 생성 및 초기화

-> 이와 관련된 기능은 ApplicationContext라는 인터페이스에 정의되어 있는데, AnnotationConfigApplicationContext 클래스느 이 인터페이스를 구현한 클래스 중 하나이다.

<img src="https://lh5.googleusercontent.com/zxqmsaVzR3xAUt5LZ99rdUgYnw_zR3U0Vt39PROmD8kd7GqO4mh1vZ9nMstnpBYck3AReb_bDqF7Qwur7CWzRAkvwo-ahdBv5a228rtbtcnr-yOCL_NRk8OWDIHxL6qLovq_kEE0" alt="img" style="zoom:67%;" />

​						-> 이보다 더 많은 인터페이스 존재



##### AnnotationConfigApplicationContext

- 자바 클래스에서 정보를 읽어와 객체 생성과 초기화 수행
- 자바 어노테이션을 이용하여 클래스로부터 객체 설정 정보를 가지고 온다



##### GenericXmlApplicationContext 

- XML로부터 객체 설정 정보를 가져온다



##### GenericGroovyApplicationContext

- 그루비 코드를 이용해 설정 정보를 가져온다



##### BeanFactory 인터페이스

- 객체 생성과 검색에 대한 기능 정의

  ex) 생성된 객체를 검색하는데 필요한 getBean() 메서드가 BeanFactory에 정의되어 있다

  ex) 싱글톤/프로토타입 빈인지 확인하는 기능



##### ApplicationContext 인터페이스

- 메시지, 프로필/환경 변수 등을 처리할 수 있는 기능을 추가로 정의

```
ApplicationContext(또는 BeanFactory)는 빈 객체의 생성, 초기화, 보관, 제거 등을 관리하고 있어서 컨테이너(Container)라고도 부른다.
```

```
스프링 컨테이너(Spring Container)

스프링 컨테이너는 내부적으로 빈 객체와 빈 이름을 연결하는 정보를 갖는다.
예를 들어 Greeter 타입의 객체를 greeter라는 이름의 빈으로 설정했을 경우, greeter라는 이름과 Greeter 객체를 연결한 정보를 관리한다.

뿐만 아니라 실제 객체의 생성, 초기화, 의존 주입 등 스프링 컨테이너는 객체 관리를 위한 다양한 기능을 제공한다. 
```





---------

### 싱글톤 객체

![img](https://lh3.googleusercontent.com/jKFhLmAK0Itzl4z2A8eflSC5Cg5kgYGsXXfT6RBsH9Y_8LjlTKwlTFAlY-9oyLZQbFprQz6H47XAxC1hKY0uCDZA1AOHm1z4q-L4NHhrcva0Af4aBT6_79HWhXrdoOFs3GizQ1IU)

![img](https://lh4.googleusercontent.com/hLbihxsbI2U9qNqQsopQHdzK5j_zHPraeMRAGNQ3-tkmNIJUwLjqQXni7Nc9FGpi34TskbR7i6RAHPEJa6dtM-DroB30ke_r7gFH0WQvZSy_XOxZHCnf9SQqS8C5kbHkSZOqPtZx)



- 별도의 설정을 하지 않을 경우 스프링은 한 개의 빈 객체만 생성하며, 이때 빈 객체는 싱글톤 범위를 갖는다고 표현한다.









