# InfluxDB

## InfluxDB란?

InfluxDB란 많은 쓰기 작업과 쿼리 부하를 처리하기 위해 2013년에 Go 언어로 개발된 오픈소스 Time Series Database(시계열 데이터베이스)로써 Tick Stack(Telegraf + InfluxDB + Chronograf + Kapacitor)의 필수 컴포넌트 중 하나입니다. 

InfluxDB는 많은 TSDB(Prometheus, TimescaleDB, Graphite 등) 중에서 가장 유명하고, 많이 사용되는 데이터베이스입니다. InfluxDB는 Distributed, Scale horizontally 하게 설계되어 새로운 노드만 추가하면 손쉽게 scale-out 할 수 있으며, Restful API를 제공하고 있어 API 통신이 가능합니다. 

### Tick Stack

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0a218627-5278-4436-9c91-44b74f5831f3/Untitled.png)

- Telegraf : Metrics와 Events를 수집하고 리포팅하는 모듈
- InfluxDB : Time Series DB
- Kapacitor : Real-time 스트리밍 데이터 전송 엔진
- Chronograf : 시각화 도구

## InfluxDB 핵심 기능 (Continuous Query, Retention Policy)

InfluxDB는 2가지 핵심 기능을 제공하는데, 그것은 바로 일정 주기마다 데이터를 처리하여 새롭게 저장하는 기능과 일정 주기마다 데이터를 자동으로 삭제하는 기능입니다. 

### [Continous Query(연속적인 쿼리), Task]

InfluxDB의 핵심 목적은 **시간에 따른 데이터(시계열 데이터)의 처리**입니다. 

InfluxDB는 데이터를 처리하여 새롭게 저장하는 Down Sampling(다운 샘플링)을 일정 주기마다 실행되도록 하는 Continuous Query를 제공합니다. 

InfluxDB2에서는 Continuous Query를 대체하는 Task를 제공합니다. 

### [Retention Policy(보존 정책), Retention Period]

InfluxDB의 핵심 목적은 시간에 따른 데이터의 삽입과 조회이므로 직접 Delete를 이용하는 경우는 거의 없습니다. 하지만 데이터가 계속해서 쌓이면 저장 공간 및 처리 속도 등에 문제가 생기므로 데이터를 자동으로 삭제해주는 Retention Policy(보존 정책)을 지원합니다.

Retention Policy란 오래된 데이터를 자동으로 삭제해주는 정책으로 데이터베이스 단위로 정의되며 일반적으로 1개의 데이터베이스는 여러 개의 보존 정책을 가지고 있습니다. 

만약 별도의 설정을 하지 않았다면 **autogen**이라는 기본 정책으로 적용 됩니다. (autogen은 보존 기간이 무제한이므로, 별도의 설정을 하지 않으면 데이터가 계속해서 쌓여 문제가 발생됩니다. 그러므로 별도의 설정을 통해 오래된 데이터를 관리하는 작업이 필요합니다)

InfluxDB2에서는 Retention Policy를 대체하는 Retention Period를 제공합니다. 

## InfluxDB 내부 구조 및 구성

| InfluxDB | RDB |
| --- | --- |
| Database | Database |
| Measurement | Table |
| Key | Column |
| Tag Key(String Only) | Indexed Column |
| Field Key | Unindexed Column |
| Point | Row |

### Measurement

시계열 데이터베이스에서 측정의 의미를 가지는 measurement는 관계형 데이터베이스의 table에 해당하며, RDB와 마찬가지로 하나의 데이터베이스 안에 여러 개의 measurement가 있을 수 있습니다. 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c395def7-f3ac-43f5-b18d-5aeef343ef21/Untitled.png)

RDB와 차별화된 핵심 컨셉이 있는데, InfluxDB는 NoSQL의 개념을 바탕으로 만들어져서 ******************************************************************************************************RDB와 다르게 Schemaless(스키마가 없음)****************************************************************************************************** 하다는 점입니다. 

기존의 RDB로 개발할 때에는 필요한 컬럼들의 길이 및 타입 등을 설계하면서 테이블의 스키마를 구성해야 했지만, InfluxDB는 Schemaless 데이터베이스이므로, 새로운 데이터를 추가하는 시점에 Measurement와 관련된 컬럼들이 추가되면 이러한 이유로 스키마의 변경이 매우 빠릅니다. InfluxDB는 이러한 구조를 가져감으로써 시계열 데이터에 유연하게 대처할 수 있습니다. 

### Key(Tag Key, Field Key, Time Key)

컬럼의 구성은 기존의 RDB와 약간 차이가 있습니다. 기존의 RDB에서는 1개의 컬럼에 1개의 데이터가 저장되는데, TSDB에서는 컬럼이 3가지 종류로 나뉘어지며, 각각의 컬럼은 (Key, Value)로 구성됩니다. 

- Tag Key
    - RDB에서 Indexed 컬럼과 유사하며, 인덱싱 되어 Select 문으로 조회하는 기준이 됩니다.
    - Tag의 value로는 String 타입만 가능하므로 질의 시 따옴표(’ ‘)로 감싸주어야 합니다.
- Field Key
    - RDB에서 Indexed 되지 않는 컬럼과 유사하며, 저장되는 데이터는 최소 1개 이상의 field가 필요합니다.
    - Field value는 strings, floats, integer or Boolean 타입이 가능하며, 타입이 정해지면 변경할 수 없습니다.
- Time key
    - UTC를 기준으로 1970년 1월 1일 0시 0분 0초로부터 지난 시간을 microseconds 단위로 자동 입력됩니다.
    - 수동으로 설정할 수도 있지만 권장하지 않습니다.

데이터를 저장하기 위해서는 태그 또는 필드 중 하나를 선택해야 하는데, 이것은 매우 중요합니다. 

왜냐하면 **태그에 있는 값들은 인덱싱** 되는 반면에 **필드는 인덱싱 되지 않기** 때문입니다. 그러므로 **조회를 하기 위해 인덱싱이 되어야 하는 경우에는 태그를, 그렇지 않고 단순 데이터인 경우에는 필드**로 선택하면 됩니다. 

만약 불필요한 데이터까지 모두 태그로 잡으면 인덱스 저장소가 비대해져 메모리를 많이 사용하게 되고 처리 성능이 떨어지게 될 것입니다. 

### Series

Series는 InfluxDB에만 존재하는 개념으로 **조합 가능한 Tag Key의 집합**에 해당합니다. 예를 들어 Member라는 Measurement에 (이름, 나이)가 Tag Key로 지정되어 있다면 저장된 모든 데이터들 중에서 가능한 (이름, 나이)의 집합이 Series 입니다. 

### Shard와 Shard Group

Shard Group은 InfluxDB bucket(시계열 데이터가 저장되는 위치)에 속하며 Shard Group Duration(샤드 그룹 보존 기간)에 따라 실제 데이터를 저장하는 Shard를 관리합니다. Shard에는 Shard Group에 정의된 기간에 속한 데이터들이 인코딩 및 압축되어 저장됩니다. **특정 Shard Group Duration에 저장된 모든 포인트(데이터)들은 동일한 Shard에 저장**됩니다. 

1개의 Shard는 여러 개의 Series, 디스크에 Time-Structured Merge Tree(TSM)으로 구성됩니다. **TSM은 내부적으로 사용되는 트리 기반의 데이터 저장소**라고 생각하면 됩니다. 

데이터가 TSM에 저장되기 전에는 메모리에 있고, Shard Group Duration(샤드 그룹 보존 기간)이 만료되면 디스크에 TSM 형태로 저장됩니다. 

InfluxDB는 Shard Group과 Shard를 이용해 데이터를 샤딩하여 데이터가 시간이 지남에 따라 증가하여도 처리량과 전체 성능을 높이는 접근 방법을 취합니다. 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/83264da7-c538-4bbb-b164-a41b6e2b0af5/Untitled.png)

시간을 기반으로 분리되는 Shard를 이용함으로써 시간을 제약조건으로 사용하여 메모리에 로드 할 데이터의 양 자체를 줄일 수 있습니다. 이는 모든 TSDB들이 기본적으로 가져가는 개념입니다.

### Shard Group Duration

Shard Group Duration이란 각 샤드 그룹이 메모리에 보존되고, 새롭게 생성되는 기간을 의미합니다. 만약 해당 보존 기간이 지나면 샤드 그룹은 제거됩니다. 

기본적으로 InfluxDB는 버킷의 Retention Period에 따라 샤드 그룹 보존 기간을 설정합니다. (기본 샤드 그룹 보존 기간은 7일입니다)

| Retention Period (보존 기간) | Shard Group Duration(샤드 그룹 보존 기간) |
| --- | --- |
| less than 2 days | 1시간 |
| between 2 days and 6 months | 1일 |
| greater than 6 months | 7일 |

## Retention Period와 Shard Group Duration

Retention Period와 Shard Group Duration 개념에 대해 헷갈릴 수 있습니다. 

Shard Group Duration이 예를 들어 7일이라고 할 때, 해당 기간 동안에는 데이터가 ******************************************메모리에 상주 해 있다가 7일 이후 데이터가 메모리가 아닌 디스크로 저장되는 것******************************************입니다. 그러므로 Retention Period가 무한하다면 데이터는 영구적으로 저장되지만 Shard Group Duration이 지난 경우 데이터를 메모리가 아닌 디스크에서 불러와야 합니다. 

[https://mangkyu.tistory.com/m/190](https://mangkyu.tistory.com/m/190)
