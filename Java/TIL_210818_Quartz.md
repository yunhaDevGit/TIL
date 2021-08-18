# Quartz Job Scheduler

## 1. Quartz란?
- Quartz는 Job scheduling 라이브러리이다. 완전히 자바로 개발되어 어느 자바 프로그램에서도 쉽게 통합해서 개발할 수 있다. Quartz는 수십에서 수천 개의 작업도 실행 가능하며 간단한 interval 형식이나 Cron 표현식으로 복잡한 스케줄링도 지원한다.
 

## 2. 장단점
### 장점

- DB 기반으로 스케줄러 간의 clustering 기능을 제공
- 시스템 Fail-over와 Random 방식의 로드 분산 처리를 지원
- In-memory Job Scheduler 제공
- 여러 기본 Plug-in 제공
- ShutdownHookPlugin - JVM 종료 이벤트를 캐치해서 스케줄러에게 종료를 알려준다
- LoggingJobHistoryPlugin - Job 실행에 대한 로그를 남겨 디버깅할 때 유용하게 사용할 수 있다.

### 단점
- Clustering 기능을 제공하지만, 단순한 random 방식이라서 완벽한 Cluster 간의 로드 분산은 안된다
- admin UI를 제공하지 않는다
- 스케줄링 실행에 대한 History는 보관하지 않는다
- Fixed Delay 타입을 보장하지 않으므로 추가 작업이 필요하다
 
