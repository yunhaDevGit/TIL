# DAO / Respository

## DAO

- 퍼시스턴트 로직인 Entity Bean을 대체하기 위해 만들어진 개념이다
- DAO의 인터페이스는 데이터베이스의 CRUD 쿼리와 1:1 매칭되는 세밀한 단위의 연산을 제공한다. 반면 repository는 메모리에 로드된 객체 컬렉션에 대한 집합 처리를 위한 인터페이스를 제공한다.
  - DAO가 제공하는 연산이 Repository가 제공하는 연산보다 더 세밀하며, 결과적으로 repository에서 제공하는 하나의 연산이 DAO의 여러 연산에 매핑되는 것이 일반적이다.
    - 하나의 repository 내부에서 다수의 dao를 호출하는 방식으로 repository를 구현할 수 있다.
- 일반적으로 DAO는 단일 데이터의 접근 및 갱신 개념이고, 서비스는 하나 이상의 DAO를 이용(조합)하여 비즈니스 로직을 수행
  - Controller는 업무 단위, Service는 트랜잭션 단위, DAO는 DB SQL단위





## Repository

- Domain-Driven Design의 기본 빌딩 블록 중 하나로 도메인 레이어에 객체 지향적인 컬렉션 관리 인터페이스를 제공하기 위해 사용되는 PURE FABRICATION이다. 
- 변경의 빈도가 비슷하고, 동시 접근에 대한 잠금의 단위가 되는 집합 별로 하나의 REPOSITORY를 사용한다
- repository는 특정 객체의 집합의 가상적인 메모리 컬렉션을 관리한다.
- repository를 인터페이스와 구현부로 분리한 후 인터페이스는 도메인 레이어에 속하도록 하고, 구현부는 퍼시스턴트 레이어에 속하도롣 하는 것





### DAO - persistence layer

- Persistence Layer - 영구 데이터를 빼내어 객체화 시키며, 영구 저장소에 데이터를 저장, 수정, 삭제하는 계층이다. 데이터베이스나 파일에 접근하여 데이터를 CRUD하는 계층
- DAO는 **데이터베이스**와 관련이  많으며 **Table** 중심이다



### Repository - domain layer

- domain layer - 관계형 데이터베이스의 엔티티와 비슷한 개념을 가지는 것으로 데이터 객체
- Repository는 **도메인**과 관련이 많으며 **Aggregate Roots** 만을 다룬다





***Respository는 여러 DAO를 사용하여 구현될 수 있지만, 그 반대의 경우는 불가능하다***





### DAO와 Repository의 차이점

- DAO는 데이터 지속성의 추상화이지만 Repository는 객체 컬렉션의 추상화이다
- DAO는 데이터 소스에 더 가까운 하위개념이다. Repository는 도메인 개체에 가까운 상위 개념이다
- DAO는 쿼리를 숨기는 데이터 매핑/액세스 계층이다. 그러나 Repository는 도메인과 데이터 액세스 사이의 계층으로 데이터를 수집하고 도메인 개체를 준비하는 복잡성을 숨긴다
- DAO에서 Repository를 사용할 수 없지만, Repository에서는 기본 스토리지에 접근하기 위해 DAO를 사용할 수 있다
- 빈약한 도메인이 있다면 Repository와 DAO는 같은 역할을 한다
  - 빈약한 도메인 모델은 도메인 객체들에 비즈니스 로직이 거의 없거나 아예 없는 소프트웨어 도메인 모델이다. 





그럼....만약에 user라는 사용자 정보가 있는 table이 있다고 하자.

회원가입할 때, user 테이블에서 정보 조회해서 사용자가 없으면 회원가입을 할 수 있도록 하기 위해 사용자 정보를 가지고 오는 쿼리문을 만들었을 때, 만약 dao로 만들었다면 UserDao... repository로 만들었다면 JoinRepository....? 회원가입이라는 도메인 기준으로,,,,



참조 : http://aeternum.egloos.com/1160846

​		  https://devlopsquare.tistory.com/106

