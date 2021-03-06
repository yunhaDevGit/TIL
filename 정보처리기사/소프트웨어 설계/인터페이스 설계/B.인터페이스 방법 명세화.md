# 인터페이스 방법 명세화

- 내외부 시스템이 연계하여 작동할 때 인터페이스별 송수신 방법, 송수신 데이터, 오류 식별 및 처리 방안에 대한 내용을 문서로 명확히 정리하는 것
- 인터페이스별로 송수신 방법을 명세화 하기 위해 **시스템 연계 기술, 인터페이스 통신 유형, 처리 유형, 발생 주기 등에 대한 정보가 필요하다**



#### 시스템 연계 기술

- 개발할 시스템과 내외부 시스템을 연계할 때 사용되는 기술
  - DB Link : DB에서 제공하는 DB Link 객체 이용
  - API/Open API : 송신 시스템의 데이터베이스에서 데이터를 읽어 와 제공하는 애플리케이션 프로그래밍 인터페이스 프로그램
  - 연계 솔루션 : EAI 서버와 송수신 시스템에 설치되는 클라이언트를 이용하는 방식
  - Socket : 서버는 통신을 위한 소켓을 생성하여 포트를 할당하고 클라이언트의 통신 요청 시 클라이언트와 연결하여 통신하는 네트워크 기술
  - Web Service : WSDL, UDDI, SOAP 프로토콜을 사용하여 연계하는 서비스



#### 인터페이스 통신 유형

- 개발할 시스템과 내외부 시스템 간 데이터를 송수신 하는 형태
  - **단방향** : 시스템에서 거래를 요청만 하고 응답이 없는 방식
  - **비동기** : 거래 요청 후 다른 작업 수행하다 응담이 오면 처리
  - **동기** : 거래 요청 후 응답이 올 때까지 대기



#### 인터페이스 처리 유형

- 송수신 데이터를 어떤 형태로 처리할 것인지에 대한 방식
  - **실시간 방식** : 사용자가 요청한 내용을 바로 처리해야 할 때 사용하는 방식
  - **지연 처리 방식** : 데이터를 매건 단위로 처리할 경우 비용이 많이 발생할 때 사용하는 방식
  - **배치 방식** : 대량의 데이터를 처리할 때 사용하는 방식





#### 인터페이스 발생 주기

- 개발할 시스템과 내외부 시스템 간 송수신 데이터가 전송되어 인터페이스가 사용되는 주기
  - 업무의 성격과 송수신 데이터 전송량 고려
    - 매일, 수시, 주 1회 등...



#### 송수신 방법 명세화

- 내외부 인터페이스 목록에 있는 각각의 인터페이스에 대해 연계 방식, 통신 및 처리 유형, 발생 주기 등의 송수신 방법을 정의하고 명세 작성
- 연계 방식, 통신 유형, 연계 처리 형태는 **시스템 인터페이스 설계 시 작성한 아키텍처 정의서를 기반으로 작성 **

| 인터페이스 ID |    인터페이스 명    | 송신 시스템 | 수신 시스템 | 연계 방식 | 통신 유형 | 연계처리 형태 | 연계 주기 |
| :-----------: | :-----------------: | :---------: | :---------: | :-------: | :-------: | :-----------: | :-------: |
|    IFD-001    |   지급 정보 전송    |    회계     |  길벗은행   |    EAI    | 요청/응답 |    실시간     |   매일    |
|    IFD-002    | 은행 수금 내역 수신 |    회계     |  길벗은행   |    EAI    | 요청/응답 |    실시간     |   수시    |



#### 송수신 데이터 명세화

- 내외부 인터페이스 목록에 있는 각각의 인터ㅔㅍ이스에 대해 인터페이스 시 필요한 송수신 데이터에 대한 명세 작성
- 인터페이스별로 테이블 정의서와 파일 레이아웃에서 연계하고자 하는 테이블 또는 파일 단위로 송수신 데이터에 대한 명세 작성