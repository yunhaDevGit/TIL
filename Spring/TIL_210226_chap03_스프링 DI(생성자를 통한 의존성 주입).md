# 스프링 DI(Dependency Injection)

### 의존(Dependency Injection)



예제) 회원 가입 

```java
public class MemberRegisterService {

    private MemberDao memberDao = new MemberDao();
    
    public void regist(RegisterRequest req){
        // 
        Member member = memberDao.selectByEmail(req.getEmail());
        
        // 같은 이메일을 가진 회원이 있으면 exception
        if(member!=null){
            throw new DuplicateMemberException("dup email" + req.getEmail());           }
        
        Member newMember = new Member(req.getEmail(), req.getPassword(), req.getName(),
                                      LocalDateTime.now());
        memberDao.insert(newMember);
    }
}
```

MemberRegisterService 클래스가 DB 처리를 위해 MemberDao 클래스를 사용한다. 

회원 데이터가 있는지 확인 하기 위해 MemberDao의 selectByEmail 메서드를 실행하고, 회원 데이터를 DB에 넣기 위해 insert 메서드를 실행한다.

:arrow_forward: 이렇게 한 클래스가 다른 클래스의 메서드를 실행할 때 **의존**한다고 표현한다.

``` 
의존
- 변경에 의해 영향을 받는 관계
ex) MemberDao의 insert()메서드의 이름을 insertMethod()로 바꾸면 이를 사용하는 클래스에서도 메서드 이름을 함께 변경해야 한다.
```

의존하는 대상이 있으면 해당 대상을 구하는 방법이 필요하다.



- 의존 대상 객체를 직접 생산하는 방법

  ```java
  private MemberDao memberDao = new MemberDao();
  ```

  - 의존 대상인 MemberDao의 객체를 직접 생성해 필드에 할당.
  - 객체 생성은 쉽지만 유지보수 관점에서 문제가 발생할 수 있다. (추후에...)

- DI를 통한 방법

  - DI는 의존하는 객체를 직접 생성하는 대신 의존 객체를 전달받는 방식을 사용한다.

    ```java
    public class MemberRegisterService {
        
        public void regist(RegisterRequest req){
            private MemberDao memberDao;
            
            // 의존 객체를 직접 생성하지 않고, 생성자를 통해 의존 객체를 전달 받는다
            public MemberRegisterService(MemberDao memberDao){
                this.memerDao = memberDao;
            }
            
            public Long regist(RegisterRequest req){
                Member member = memberDao.selectByEmail(req.getEmail());
                if(member!=null){
                    throw new DuplicateMemberException("dup email" + req.getName());
                }
                Member newMember = new Member(req.getEmail(), req.getPassword(), req.getName(), 
                                              LocalDateTime.insert(newMember));
                memberDao.insert(newMember);
                return newMember.getId();
            }
        }
    }
    ```

    MemberRegisterService 객체를 생성할 때, 생성자에 MemberDao 객체를 전달해야 한다. 

    ```java
    MemberDao dao = new MemberDao();
    MemberRegisterService svc = new MemberRegisterService(dao);
    ```

    :pencil2: 직접 의존 객체를 생성하면 되는데 왜 의존하는 객체를 주입하나?

    - 변경의 유연함 때문이다.(예제는 책에 나와있다. )
      - 직접 객체를 생성하여 작성하였을 경우, 변경사항이 생겼을 때 일일이 다 수정해줘야 한다
      - 생성자를 통해 의존성을 주입했을 경우, 변경사항이 생겨도 한군데에서만 수정하면 된다.





예제 코드

https://github.com/yunhaDevGit/spring5_programming/tree/master/chap03/src/main/java/DI_example

