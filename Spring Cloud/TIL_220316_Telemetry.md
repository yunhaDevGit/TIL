# Telemetry

MSA에서는 상당수의 마이크로 서비스가 분산 환경에서 운영되기 때문에 서비스들의 상태를 일일이 모니터링하고, 이슈에 대응하는 것은 굉장히 힘들고 오래 걸립니다. 

Telemetry는 서비스들을 모니터링하고, 서비스 별로 발생하는 이슈들에 대응할 수 있도록 환경을 구성하는 역할을 합니다.

Telemetry는 Grafana, Prometheus, EFK와 같이 오픈 소스로 직접 구현하는 방법, Datadog와 같은 상용 솔루션을 이용하는 방법, AWS Cloud watch, GCP Stackdriver와 같이 public cloud의 SaaS를 이용하는 방법으로 구현할 수 있습니다.

![image](https://user-images.githubusercontent.com/74949294/158789400-36cfbc2a-5ca2-4ce5-bc4f-4fee06ed1cfb.png)


## Telemetry 주요 기능

### 1. Monitoring

모니터링은 인프라 및 응용 프로그램의 성능이나 효율성을 확인하는 작업으로 마이크로 서비스를 운영하는데 필요합니다. Monitoring을 위해서는 metric 수집, logging, Tracing 영역에서의 데이터 수집 및 분석이 필요합니다. 

### 2. Logging

Log는 실행 중인 프로세스에서 발생하는 이벤트를 말하며, Logging은 이러한 Log들을 수집해서 보여주는 것을 말합니다. 

MSA는 Monolithic 애플리케이션보다 실행하는 프로세스의 수가 훨씬 많기 때문에 장애가 발생 시에 root cause를 파악하기 힘듭니다. 

**Logging 시 고려 사항**

- 로그를 서비스 내 저장소에 저장할 경우, 관리가 어려워지므로 로그를 애플리케이션 서버 내부에 저장하지 않고 외부 저장소에 저장해야 합니다.
- MSA에서 가상 머신과 컨테이너는 application과의 결합이 “약한 결합”입니다. 즉, 배포에 사용되는 컨테이너는 배포할 때마다 달라질 수 있습니다. 따라서 디스크 저장 상태에 의존하는 것이 아니라, 별도의 Logging 도구를 활용하는 것이 효율적입니다.
- 로그 파일은 ACL을 통해 로그 관리자만 로그를 확인할 수 있도록 해야 합니다.

### 3. Tracing

Tracing은 MSA에서 발생한 Event를 발생한 순서대로 나열하여 추적할 수 있게 해주는 기능입니다.
