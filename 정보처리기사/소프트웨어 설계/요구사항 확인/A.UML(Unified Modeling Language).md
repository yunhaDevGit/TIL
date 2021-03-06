# UML(Unified Modeling Language)

- 시스템 분석, 설계, 구현 등 시스템 개발과정에서 이해관계자들 간의 의사소통이 원활하게 이뤄지도록 **표준화된 대표적인 객체지향 모델링 언어**
  - OMG에서 표준으로 지정
  - UML의 구성 요소 : 사물, 관계, 다이어그램 등,,,



#### 사물

- 다이어그램 안에서 관계가 형성될 수 있는 대상

| 사물      | 내용                                                         |
| --------- | ------------------------------------------------------------ |
| 구조 사물 | - 시스템의 개념적 물리적 요소를 표현<br />- 클래스, 유스케이스, 컴포넌트, 노드 등 |
| 행동 사물 | - 시간과 공간에 따른 요소들의 행위를 표현<br />- 상호작용, 상태 머신 등 |
| 그룹 사물 | - 요소들을 그룹으로 묶어서 표현<br />- 패키지                |
| 주해 사물 | - 부가적인 설명이나 제약조건 등을 표현<br />- 노트           |



#### 관계

- 사물과 사물 사이의 연관성을 표현하는 것
- 연관관계, 집합관계, 포함관계, 일반화 관계, 의존 관계, 실체화 관계 등..

##### 연관관계

- 2개 이상의 사물이 서로 관련 있음을 표현
  - 사물 사이를 실선으로 연결. 방향성은 화살표로 표현
  - 양방향일 경우 화살표 생략
  - 참여하는 객체의 개수를 의미하는 **다중도는** 선 위에 표기

| 다중도     | 의미                                     |
| ---------- | ---------------------------------------- |
| 1          | 1개의 객체가 연관 되어 있다              |
| n          | n개의 객체가 연관 되어 있다              |
| 0..1       | 연관된 객체가 없거나 1개만 존재          |
| 0..* or  * | 연관된 객체가 없거나 다수일 수 있다      |
| 1..*       | 연관된 객체가 적어도 1개 이상이다        |
| n..*       | 연관된 객체가 적어도 n개 이상이다        |
| n..m       | 연관된 객체가 최소 n개에서 최대 m개이다. |

**예시 책 참고!!!**



#### 집합 관계

- 하나의 사물이 다른 사물에 포함되어 있는 관계
- 포함하는 쪽과 포함되는 쪽은 **서로 독립적**이다
- 포함되는 쪽에서 포함하는 쪽으로 **속이 빈 마름모**를 연결하여 표시
  - ex) 프린터는 컴퓨터에 연결해서 사용할 수 있고, 다른 컴퓨터에도 연결할 수 있다.



#### 포함 관계

- 집합 관계의 특수한 형태로, **포함하는 사물의 변화가 포함되는 사물에게 영향을 미치는 관계**를 표현
- 포함하는 쪽과 포함되는 쪽은 **서로 독립적일 수 없고, 생명주기를 함꼐한다**
- 포함되는 쪽에서 포함하는 쪽으로 **속이 찬 마름모**를 연결하여 표시
  - ex) 문을 열수 있는 키는 하나며, 해당 키로 다른 문을 열 수 없다. 문이 없어지면 키도 필요 없다.



#### 일반화 관계

- 하나의 사믈이 다른 사물에 비해 **더 일반적인지 구체적**인지를 표현
  - ex) 사람은 여자와 남자보다 일반적인 개념이고, 반대로 남자와 여자는 사람보다 구체적인 개념이다.
- 일반적인 개념 : 상위(부모) / 구체적인 개념 : 하위(자식)
- 하위에서 상위로 **속이 빈 화살표를 연결**
  - ex) 아메리카노와 에스프레소는 커피이다. 커피에는 에스프레소와 아메리카노가 있다. 



#### 의존 관계

- 연관관계와 같이 사물 사이에 **서로 연관은 있으나 필요에 의해 서로에게 영향을 주는 짧은 시간 동안만 연관을 유지하는 관계**
  - 하나의 사물과 다른 사물이 소유관계는 아니지만 **사물의 변화가 다른 사물에도 영향을 미치는 관계**
  - 영향을 주는 사물이 **영향을 받는 사물 쪽으로 점선 화살표 연결**하여 표현
    - ex) 등급이 높으면 할인율을 적용하고, 등급이 낮으면 할인율을 적용하지 않는다.



#### 실체화 관계

- 사물이 할 수 있거나 **해야 하는 기능(행위, 인터페이스)으로 서로를 그룹화할 수 있는 관계**

- 사물에서 기능 쪽으로 **속이 빈 점선 화살표**를 연결하여 표현

  - ex) 새와 비행기는 날 수 있다는 행위로 그룹화 될 수 있다

  

### 다이어그램

- 사물과 관계를 도형으로 표현
  - 여러 관점에서 시스템을 가시화한 뷰를 제공함으로써 의사소통에 도움을 준다
  - ***정적 모델링***에서는 주로 **구조적 다이어그램**을 사용하고 ***동적 모델링***에서는 주로 **행위 다이어 그램** 사용



#### 구조적 다이어그램

- 클래스 다이어그램
  - 클래스의 속성 및 클래스 사이의 관계 표현
  - 시스템 구조 파악 및 구조상의 문제점 도출 가능
- 객체 다이어그램
  - 클래스에 속한 인스턴스를 특정 시점의 객체와 객체 사이의 관계로 나타낸다
- 컴포넌트 다이어그램
  - 실제 구현 모듈인 컴포넌트 간의 관계나 컴포넌트 간의 인터페이스 표현
  - **구현 단계에서 사용**하는 다이어그램
- 배치 다이어그램
  -  결과물, 프로세스, 컴포넌트 등 물리적인 요소들의 위치 표현
  - **구현 단계에서 사용**
- 복합체 구조 다이어그램
  - 클래스나 컴포넌트가 복합 구조를 갖는 경우 그 내부 구조 표현
- 패키지 다이어그램
  - 유스케이스나 클래스 등의 모델 요소들을 그룹화한 패키지들의 관계를 표현



#### 행위 다이어그램

- 유스케이스 다이어그램
  - **사용자의 요구를 분석**하는 것으로 기능 모델링 작업에 사용
  - 사용자와 사용 사례로 구성되며, **사용 사례 간의 여러 형태의 관계**로 이뤄진다
- 시퀀스 다이어그램
  - 상호 작용하는 시스템이나 객체들이 주고 받는 메시지를 표현
- 커뮤니케이션 다이어그램
  - 시퀀스 다이어그램과 같이 동작하는 객체들이 주고 받는 메시지를 표현
  - 객체들 간의 연관 관계 표현
- 상태 다이어그램
  - 하나의 객체가 자신이 속한 클래스의 상태 변화 혹은 다른 객체와의 상호 작용에 따라 상태 변화를 표현
- 활동 다이어그램
  - 시스템이 어떤 기능을 수행하는지 객체의 처리 로직이나 조건에 따른 처리의 흐름을 순서에 따라 표현
- 상호 작용 개요 다이어그램
  - 상호작용 다이어그램 간의 제어 흐름 표현
- 타이밍 다이어그램
  - 객체 상태 변화와 시간 제약을 명시적으로 표현