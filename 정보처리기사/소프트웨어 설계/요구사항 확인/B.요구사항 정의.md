# 요구사항 정의

### 요구사항이란?

- 소프트웨어가 제공하는 서비스에 대한 설명과 정상적으로 운영되는데 필요한 제약 조건
  - 소프트웨어 개발이나 유지 보수 과정에서 필요한 기준과 근거를 제공
  - 개발하려는 소프트웨어의 전반적인 내용을 확인할 수 있게 한다.
    - -> **의사소통을 원활**하게 하는데 도움을 준다
  - 요구사항이 제대로 정의되어야만 이를 토대로 이후 과정의 목표와 계획을 수립할 수 있다



#### 요구사항 유형

**기능 요구사항(Functional requirements)**

- 시스템이 무엇을 하는지, 어떤 기능을 하는지에 대한 사항
- 시스템의 입출력에 무엇이 포함되어야 하는지, 시스템이 연산하는 데이터에 대한 사항
- 시스템이 반드시 수행해야 하는 기능(사용자가 제공받길 원하는 기능)



**비기능 요수사항(No-Functional Requirements)**

- 시스템 장비 구성 요구사항 : 하드웨어, 소프트웨어, 네트워크 등 시스템 장비 구성에 대한 요구사항

- 성능 요구사항 : 처리 속도 및 시간, 처리량, 동적/정적 적용량 등 성능에 대한 요구사항

- 인터페이스 요구사항 : 시스템 인터페이스와 사용자 인터페이스에 대한 요구사항

  (다른 sw, hw 및 통신 인터페이스, 다른 시스템과의 정보 교환 )

- 데이터 요구사항 : 초기 자료 구축 및 데이터 변환을 위한 대상, 방법, 보안이 필요한 데이터 등 데이터를 구축하기 위한 요구사항

- 보안 요구사항 : 시스템 데이터 및 기능, 접근을 통제하기 위한 요구사항

- 품질 요구사항 : 관리가 필요한 품질 항목, 품질 평가 대상에 대한 요구사항

  (가용성, 상호 호환성, 대응성, 신뢰성 등으로 구분해서 기술)

- 제약 사항 : 시스템 설계, 구축, 운영과 관련하여 사전에 파악된 기술, 표준 업무 등의 제약 조건



**사용자 요구사항(User Requirements)**

- 사용자 관점에서 본 시스템이 제공해야 할 요구사항
- 사용자를 위한 것으로 친숙한 표현으로 이해하기 쉽게 작성!



**시스템 요구사항(System Requirements)**

- 개발자 관점에서 본 시스템이 제공해야 할 요구사항
- 사용자 요구사항에 비해 전문적이고 기술적인 용어로 표현
- 소프트웨어 요구사항이라고도 한다



### 요구사항 개발 프로세스

- 개**발 대상에 대한 요구사항을 체계적으로 도출하고 확인**하는 일련의 구조화된 활동이다

- 요구사항 개발 프로세스 진행 전 개발 프로세스가 **비즈니스 목적에 부합되는지**, **예산은 적정한지** 등에 대한 정보 수집한 보고서를 토대로 **타당성 조사가 선행**되어야 한다.

- 과정

  - 도출 -> 분석 -> 명세 -> 확인

  

#### 요구사항 도출

- 시스템, 사용자, 그리고 시스템 개발에 관련된 사람들이 **서로 의견을 교환**하여 **요구사항이 어디에 있는지, 어떻게 수집할 것인지를 식별하고 이해하는 과정**
  - 서로간의 **효율적인 의사소통**이 중요
  - 소프트웨어 개발 생명주기동안 지속적으로 반복된다
  - 브레인스토밍, 프로토타이핑, 유스케이스, 워크샵, 인터뷰, 설문 등의 방법이 있다



#### 요구사항 분석

- 개발 대상에 대한 사용자의 **요구사항 중 명확하지 않거나 모호하여 이해되지 않는 부분을 걸러내는 과정**
  - 사용자 요구사항의 **타당성 조사**, 비용과 일정에 대한 제약 설정
  - 중복되거나 합쳐져야 할 내용이 있으면 해결
  - 도출된 요구사항을 토대로 소프트웨어 범위 파악
  - 도출된 요구사항을 토대로 소프트웨어와 주변 환경이 상호 작용하는 방법 이해



#### 요구사항 명세

- **요구사항을 체계적으로 분석 후 승인될 수 있도록 문서화** 하는 것
  - 요구사항을 문서화 할 때는 **기능 요구사항은 빠짐없이 완전하고 명확하게 기술** 해야 한다. 
  - **비기능 요구사항은 필요한 것만 명확하게** 기술
  - 요구사항은 **사용자가 이해하기 쉬우며, 개발자가 효과적으로 설계할 수 있도록 한다**
  - 설계 과정에서 오류 발견 시 해당 내용을 **요구사항 정의서에서 추적할 수 있어야 한다**



#### 요구사항 확인(검증)

- 개발 자원을 요구사항에 할당하기 전에 **요구사항 명세서가 정확하고 완전하게 작성되었는지 검토**하는 활동
  - 요구사항을 정확하게 이해 후 요구사항 명세서를 작성했는지 확인 필요
  - 요구사항 명세서의 내용이 이해가 쉬운지, 일관성은 있는지, 회사 기준에 맞는지, 누락된 기능은 없는지 등을 확인
  - 요구사항 관리도구를 이용하여 요구사항 정의 문서들에 대한 형상 관리를 수행한다