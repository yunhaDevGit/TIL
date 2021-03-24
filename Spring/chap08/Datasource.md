# Datasource

- java에서 connection pool을 지원하기 위한 인터페이스
  - connection pool : connection 맺고 끊는 작업을 줄이기 위해 미리 connection을 생성해두고 모아두는 장소
- 순수 jdbc로 데이터베이스에 접근을 하면, 데이터베이스에 접근할 때마다 connection을 맺고 끊는 작업을 한다.