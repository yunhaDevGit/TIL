# 모듈

- 모듈화를 통해 분리된 시스템의 각 기능들로 서브루틴, 서브시스템, 소프트웨어 내의 프로그램, 작업 단위 등과 같은 의미로 사용
- 모듈은 **단독으로 컴파일이 가능하며 재사용 가능** 
- 모듈이 **하나의 기능만을 수행**하고 **다른 모듈과의 과도한 상호작용을 배제**
- 모듈의 독립성은 **결함도, 응집도에 의해 측정**



#### 결합도

- 모듈간에 상호 의존하는 정도 또는 두 모듈 사이의 연관 관계를 의미
- **결합도가 약할수록 품질이 높다**
- 결합도가 낮으면 유지보수 용이
- 결합도 종류
  - (결합도 낮음)자료 결합도, 스탬프 결합도, 제어 결합도, 외부 결합도, 공통 결합도, 내용 결합도(결합도 강함)
  - 자료 결합도 : 모듈 간의 인터페이스가 **자료 요소로만 구성**
    - 가장 바람직함
  - 스탬프 결합도 : 모듈 간의 인터페이스로 배열이나 레코드 등의 **자료구조**가 전달될 때의 결합도
  - 제어 결합도 : 어떤 모듈이 다른 모듈 내부의 논리적인 흐름을 제어하기 위해 제어 신호를 이용하여 통신하거나 제어 요소를 전달하는 결합도
  - 외부 결합도 : 어떤 모듈에서 선언한 데이터를 외부의 다른 모듈에서 참조할 때의 결합도
  - 공통 결합도 : 공유되는 공통 데이터 영역을 여러 모듈이 사용할 때의 결합도
  - 내용 결합도 : 한 모듈이 다른 모듈의 내부 기능 및 그 내부 자료를 직접 참조하거나 수정할 때의 결합도



#### 응집도

- 정보 은닉 개념을 확장한 것. 
- 명령어나 호출문 등 모듈의 내부 요소들이 서로 관련되어 있는 정도
- **모듈이 독립적인 기능으로 정의되어 있는 정도**
- (응집도 ↑)기능적 응집도, 순차적 응집도, 교환적 응집도, 절차적 응집도, 시간적 응집도, 논리적 응집도, 우연적 응집도(응집도 ↓)
  - 기능적 응집도 : 모듈 내부의 모든 기능 요소들이 단일 문제와 연관되어 수행될 경우
  - 순차적 응집도 : 모듈 내 하나의 활동으로부터 나온 출력 데이터를 그 다음 활동의 입력 데이터로 사용할 경우
  - 교환적 응집도 : 동일한 입력과 출력을 사용하여 서로 다른 기능을 수행하는 구성요소들이 모였을 경우
  - 절차적 응집도 : 모듈이 다수의 관련 기능을 가질 때 모듈 안의 구성 요소들이 그 기능을 순차적으로 수행할 경우
  - 시간적 응집도 : 특정 시간에 처리되는 몇 개의 기능을 모아 하나의 모듈로 작성할 경우
  - 논리적 응집도 : 유사한 성격을 갖거나 특정 형태로 분류되는 처리 요소들로 하나의 모듈이 형성되는 경우
  - 우연적 응집도 : 모듈 내부의 각 구성 요소들이 서로 관련 없는 요소로만 구성된 경우



#### 팬인(Fan-In)/ 팬아웃(Fan-Out)

- Fan-In
  - 어떤 모듈을 제어(호출)하는 모듈의 수
- Fan-Out
  - 어떤 모듈에 의해 제어(호출)되는 모듈의 수
- 팬인과 팬아웃을 분석하여 시스템 복잡도를 알 수 있다
- 팬인 ↑ : 재사용성 ↑.단일 장애점(시스템 두성 요소가 동작하지 않으면 전체가 중단되는 현상) 발생 가능. 중점적인 관리 및 테스트 필요
- 팬 아웃 ↑ : 불필요하게 다른 모듈을 호출하고 있는지 검토하고, 단순화 시킬 수 있는지 여부에 대한 확인 필요





#### 효과적인 모듈 설계 방안

- **결합도 ↓ 응집도 ↑** 
- 모듈의 제어 영역 안에서 그 모듈의 영향 영역을 유지
- **복잡도와 중복성을 줄이고 일관성 유지**
- 모듈의 기능은 **예측 가능해야 하며 지나치게 제한적이면 안된다**
- **유지보수 용이**
- 시스템의 전반적인 기능과 구조를 이해하기 쉬운 크기로 분해
- 하나의 입구와 하나의 출구를 갖도록