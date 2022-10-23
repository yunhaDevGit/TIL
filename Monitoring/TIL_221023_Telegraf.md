# Telegraf

Telegraf는 Go언어로 쓰여진 agent로 주로 수집, 처리, 종합 처리, 메트릭에 쓰기 위해 사용됩니다. 

Telegraf는 작은 메모리를 사용하면서 개발자들이 메트릭을 수집해서 쉽게 지원하는 것을 지향합니다. 로컬 또는 원격의 config까지 반영 시킬 수 있습니다.

Telegraf는 plugin 방식을 사용하여 4가지 플러그인 컨셉을 가지고 있습니다. 

1. **Input Plugin** : 시스템, 서비스, 써드파티 API로부터 메트릭 수집
2. **************************************Processor Plugin :************************************** 매트릭 설명, 변환 등을 지원
3. ********Aggregator Plugin******** : mean, max, min, quantiles와 같은 다양한 종합 매트릭 지원
4. ********Output Plugin******** : 다양한 목적지에 매트릭 저장

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1b40e969-c58b-4542-8269-feec0d9e36c8/Untitled.png)

Input과 Out Plugin 사이에 Process와 Aggregate가 자리하며 transform/decorate/filter를 거쳐 mean/quantiles/min,max/count와 같은 함수를 거치게 됩니다. 

여러 개의 input을 가질 수도 있고 여러 개의 output을 가질 수도 있습니다. 만약 destination을 InfluxDB만 쓰는게 아니라 Graphite와 같은 다른 시계열 Data Engine을 사용할 수도 있습니다. 

만약 Telegraf를 Collector로 쓴다면 각각 다른 시계열 Data Engine에 데이터를 보내서 성능 비교를 할 수도 있습니다. 

[https://notemusic.tistory.com/72](https://notemusic.tistory.com/72)
