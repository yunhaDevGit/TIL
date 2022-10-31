# Kapacitor API

# 기본 정보

- Kapaciator는 HTTP API를  ******9092****** 포트로 제공합니다.
- 모든 요청은 `/kapacitor/v1`와 같이 version과 namespace을 기본 경로로 사용합니다.

## HTTP 응답 코드

| 응답 코드 | 설명 |
| --- | --- |
| 2xx | 요청이 성공했으며 요청에 따라 콘텐츠는 다릅니다. |
| 4xx | 잘못된 요청입니다. 요청에 문제가 있는 경우 오류를 참조하세요. 요청을 반복하면 오류가 계속 반환 됩니다. |
| 5xx | 서버가 요청을 처리할 수 없습니다. 이유에 대한 오류를 참조하십시오. 서버 문제가 해결 된 경우 요청을 반복하면 성공할 수 있습니다.  |

## 에러

모든 요청은 실패한 요청에 대한 추가 정보 제공을 위해 아래의 형식으로 JSON을 반환합니다. 

```json
{ "error" : "error message" }
```

## Query parameters vs JSON body

쿼리 매개변수는 GET 요청에서만 사용됩니다. 다른 모든 요청에는 JSON 본문의 매개변수가 필요합니다. 

> `/kapacitor/v1/write` 는 예외 규칙으로 `/write` endpoint는 Kapacitor가 InfluxDB와 호환 되도록 합니다.
> 

## Links

리소스를 생성할 때 Kapacitor API 서버는 `href` 리소스의 `link` object를 반환합니다. 클라이언트는 경로 조작 없이 이전 호출에서 제공된 링크를 사용할 수 있어야 합니다.

## IDs

ID 제어 할 수 있도록 Kapacitor API를 사용하면 클라이언트가 다양한 리소스에 대한 ID를 지정할 수 있습니다. ID를 지정하지 않으면 Kapacitor는 리소스에 대한 임의의 UUID를 생성합니다. 

모든 ID는 아래의 정규식과 일치해야 합니다. 

```
^[-\._\p{L}0-9]+$`
```

# 데이터 쓰기

Kapacitor는 InfluxData의 `라인 프로토콜 데이터 형식`을 사용하여 HTTP를 통한 데이터 쓰기를 허용합니다. `kapacitor/v1/write` endpoint는 기본적으로 `/write` endpoint와 동일합니다. 

| 쿼리 매개변수 | 목적 |
| --- | --- |
| DB | 쓰기에 대한 데이터베이스 이름입니다. |
| rp | 쓰기에 대한 보존 정책 이름입니다. |

> Kapacitor는 데이터베이스 및 보존 정책에 따라 모든 포인트의 범위를 지정합니다. 
결과적으로 `rp` 는 Kapacitor가 올바른 보존 정책을 사용하도록 쓰기에 대해 지정해야 합니다.
> 

*******Example*******

```
POST /kapacitor/v1/write?db=DB_NAME&rp=RP_NAME
cpu,host=example.com value=97.8
```

# 작업 관리

작업은 Kapacitor가 수행할 작업을 나타냅니다. task는 id, type, TICKscript, 데이터베이스 유지 정책 등으로 정의 됩니다.

## ****************************************************************task 정의 및 업데이트****************************************************************

task 정의하기 위해서는 `/kapacitor/v1/tasks` endpoint로 POST 요청을 하면 됩니다. 만약 task가 이미 있다면 `PATCH` 메서드를 통해 task 속성을 수정하면 됩니다.

아래의 옵션을 JSON 객체에 담아 정의할 수 있습니다. 

| Property | Puropose |
| --- | --- |
| id | task의 식별자. 만약 빈 값의 경우 랜덤 ID |
| template-id | TICKscript와 type을 지정하는 대신 사용할 템플릿 ID |
| type | task 타입: stream, batch |
| dbrps | task가 접근할 수 있는 데이터베이스 유지 정책 목록 |
| script | 스크립트 내용 |
| status | enabled 또는 disabled  |
| vars | TICKscript에 정의된 변수를 덮어쓰기 위한 변수 집합 |

 

> 작업 패치 시 실행 중인 작업은 변경되지 않습니다. 변경 사항을 적용하려면 작업을 비활성화 했다가 다시 활성화해야 합니다.
> 

## vars

vars 객체 형식은 아래와 같습니다

```json
{
	"field_name" : {
		"value" : <VALUE>,
		"type" : <TYPE>
	},
	"another_field" : {
		"value" : <VALUE>,
		"type" : <TYPE>
	}
}
```

********Example********

`id`가 `TASK_ID` 인 새 task를 만듭니다. 템플릿에서 작업을 만들려면 `template-id`를 추가해야 합니다. 

```json
POST /kapacitor/v1/tasks
{
    "id" : "TASK_ID",
    "template-id" : "TEMPLATE_ID", 
    "type" : "stream",
    "dbrps": [{"db": "DATABASE_NAME", "rp" : "RP_NAME"}],
    "script": "stream\n    |from()\n        .measurement('cpu')\n",
    "vars" : {
        "var1": {
            "value": 42,
            "type": "float"
        }
    }
}
```

1) task의 `dbrps`만 수정합니다.

```json
PATCH /kapacitor/v1/tasks/TASK_ID
{
    "dbrps": [{"db": "NEW_DATABASE_NAME", "rp" : "NEW_RP_NAME"}]
}
```

> DBRP를 설정하면 저장된 모든 DBRP를 덮어씁니다. Vars를 설정하면 저장된 모든 Vars를 덮어씁니다.
> 

2) 기존 작업을 비활성화합니다.

```json
PATCH /kapacitor/v1/tasks/TASK_ID
{
    "status" : "disabled",
}
```

3) 생성 시 활성화 되는 새 작업을 정의합니다.

```json
POST /kapacitor/v1/tasks
{
    "id" : "TASK_ID",
    "template-id" : "TEMPLATE_ID",
    "type" : "stream",
    "dbrps" : [{"db": "DATABASE_NAME", "rp" : "RP_NAME"}],
    "script" : "stream\n    |from()\n        .measurement('cpu')\n",
    "status" : "enabled"
}
```

## **********응답**********

| 응답 코드 | 의미 |
| --- | --- |
| 200 | 작업이 생성되었으며 작업 정보가 포함되어 있습니다. |
| 404 | 작업이 존재하지 않습니다. |

# 작업 가져오기

작업에 대한 정보를 얻으려면 `/kapacitor/v1/tasks/TASK_ID`로 `GET` 요청을 하세요.

| 쿼리 매개변수 | 기본 | 목적 |
| --- | --- | --- |
| dot-view | attributes | labels 또는 attributes.
labels는 가독성이 떨어지지만 label에 포함된 모든 정보로 올바르게 렌더링 됩니다. |
| script-format | formatted | formatted 또는 raw.
raw는 정의 된 것과 동일한 스크립트를 반환합니다.
formatted는 먼저 스크립트를 포맷합니다. |
| replay-id |  | 반환된 작업 정보는 실행 중인 재생에 대한 작업 컨텍스트에 있습니다. |

| Property | Description |
| --- | --- |
| dot | GraphViz DOT 구문 형식의 작업 DAG 표현 |
| executing | 작업이 현재 실행 중인지 여부 |
| error | 작업을 실행할 때 발생한 모든 오류 |
| stats | 작업에 대한 통계 맵 |
| created | 작업이 처음 생성된 날짜 |
| modified | 작업이 마지막으로 수정된 날짜 |
| last-enabled | 작업이 마지막으로 enabled 설정된 날짜 |

**************Example**************

기본값을 사용하여 작업에 대한 정보를 가져옵니다. 작업이 템플릿과 연결 된 경우 템플릿 ID가 응답에 포함됩니다.

```json
GET /kapacitor/v1/tasks/TASK_ID
{
    "link" : {"rel": "self", "href": "/kapacitor/v1/tasks/TASK_ID"},
    "id" : "TASK_ID",
    "template-id" : "TEMPLATE_ID",
    "type" : "stream",
    "dbrps" : [{"db": "DATABASE_NAME", "rp" : "RP_NAME"}],
    "script" : "stream\n    |from()\n        .measurement('cpu')\n",
    "dot" : "digraph TASK_ID { ... }",
    "status" : "enabled",
    "executing" : true,
    "error" : "",
    "created": "2006-01-02T15:04:05Z07:00",
    "modified": "2006-01-02T15:04:05Z07:00",
    "last-enabled": "2006-01-03T15:04:05Z07:00",
    "stats" : {},
    "template-id": "TASK_ID"
}
```

# 작업 삭제

작업을 삭제하려면 `/kapacitor/v1/tasks/TASK_ID` 엔드포인트에 DELETE 요청을 보냅니다.

```json
DELETE /kapacitor/v1/tasks/TASK_ID
```

**응답**

| 코드 | 의미 |
| --- | --- |
| 204 | 성공 |

# 작업 리스트

여러 작업에 대한 정보를 얻으려면 `/kapacitor/v1/tasks`에 `GET` 요청을 보내세요.

| Query Parameter | Default | Purpose |
| --- | --- | --- |
| pattern |  | 패턴을 기반으로 결과를 필터링. 표준 셸 glob 매칭을 사용합니다 |
| fields |  | 반환할 필드 목록입니다. 비어 있을 경우 모든 필드를 반환합니다. id 및 link 필드는 항상 반환됩니다. |
| dot-view | attributes | label 또는 attributes.레이블은 가독성이 떨어지지만 레이블에 포함된 모든 정보는 올바르게 렌더링 됩니다. |
| script-format | formatted | formatted 또는 raw. raw는 정의된 것과 동일한 스크립트를 반환합니다. |
| offset | 0 | 작업을 통해 페이지를 매기기 위한 오프셋 수 |
| limit | 100 | 반환할 최대 작업 수 |

**************Example**************

모든 작업 목록 세부 정보를 가져옵니다.

```json
GET /kapacitor/v1/tasks

{
    "tasks" : [
        {
            "link" : {"rel":"self", "href":"/kapacitor/v1/tasks/TASK_ID"},
            "id" : "TASK_ID",
            "type" : "stream",
            "dbrps" : [{"db": "DATABASE_NAME", "rp" : "RP_NAME"}],
            "script" : "stream|from().measurement('cpu')",
            "dot" : "digraph TASK_ID { ... }",
            "status" : "enabled",
            "executing" : true,
            "error" : "",
            "stats" : {},
            "template-id" : "TEMPLATE_ID"
        },
        {
            "link" : {"rel":"self", "href":"/kapacitor/v1/tasks/ANOTHER_TASK_ID"},
            "id" : "ANOTHER_TASK_ID",
            "type" : "stream",
            "dbrps" : [{"db": "DATABASE_NAME", "rp" : "RP_NAME"}],
            "script" : "stream|from().measurement('cpu')",
            "dot" : "digraph ANOTHER_TASK_ID{ ... }",
            "status" : "disabled",
            "executing" : true,
            "error" : "",
            "stats" : {},
            "template-id" : "TEMPLATE_ID"
        }
    ]
}
```

`pattern`이 일치하는 작업만 가지고 오도록 합니다.

```json
GET /kapacitor/v1/tasks?pattern=TASK*
{
    "tasks" : [
        {
            "link" : {"rel":"self", "href":"/kapacitor/v1/tasks/TASK_ID"},
            "id" : "TASK_ID",
            "type" : "stream",
            "dbrps" : [{"db": "DATABASE_NAME", "rp" : "RP_NAME"}],
            "script" : "stream|from().measurement('cpu')",
            "dot" : "digraph TASK_ID { ... }",
            "status" : "enabled",
            "executing" : true,
            "error" : "",
            "stats" : {},
            "template-id" : "TEMPLATE_ID"
        }
    ]
}
```

모든 작업을 가져오지만 상태, 실행 및 오류 필드만 가지고 옵니다.

```json
GET /kapacitor/v1/tasks?fields=status&fields=executing&fields=error

{
    "tasks" : [
        {
            "link" : {"rel":"self", "href":"/kapacitor/v1/tasks/TASK_ID"},
            "id" : "TASK_ID",
            "status" : "enabled",
            "executing" : true,
            "error" : "",
        },
        {
            "link" : {"rel":"self", "href":"/kapacitor/v1/tasks/ANOTHER_TASK_ID"},
            "id" : "ANOTHER_TASK_ID",
            "status" : "disabled",
            "executing" : true,
            "error" : "",
        }
    ]
}
```

# 템플릿 관리

추후 추가

# 기록 관리

Kapacitor는 데이터 기록을 저장하고 지정된 작업에 대해 재생할 수 있습니다. 

## 기록 만들기

Kapacitor를 사용하여 데이터를 기록하는 방법에는 세가지가 있습니다.

`/kapacitor/v1/recordings/METHOD` endpoint로 POST 요청을 보내 생성 할 수 있습니다. 

| Method | Description |
| --- | --- |
| stream | 들어오는 데이터 스트림을 기록합니다. |
| batch | 일괄 작업에 쿼리 결과를 기록합니다. |
| query | 명시적 쿼리의 결과를 기록합니다. |

기록이 시작되면 반환되고 완료될 때까지 기다리지 않습니다. recording ID는 나중에 기록을 식별하기 위해 사용됩니다. 

### Stream

| Parameter | Purpose |
| --- | --- |
| id | 기록의 고유 식별자입니다. 비어 있을 경우 랜덤으로 선택됩니다. |
| task | 작업의 DBRP에 대한 데이터만 기록하는데 사용되는 작업의 ID입니다. |
| stop | 중지 날짜까지 스트림 데이터를 기록합니다. |

### Batch

| Parameter | Purpose |
| --- | --- |
| id | 기록의 고유 식별자입니다. 비어 있을 경우 랜덤으로 선택됩니다. |
| task | 작업 ID. 작업에 정의된 쿼리 결과를 기록합니다. |
| start | 데이터가 기록될 가장 빠른 날짜입니다. |
| stop | 데이터가 기록될 마지막 날짜입니다. 지정하지 않을 경우 현재 시간이 사용됩니다. |

### Query

| Parameter | Purpose |
| --- | --- |
| id | 기록의 고유 식별자입니다. 비어 있으면 무작위로 선택됩니다. |
| type | 기록 유형은 stream, batch |
| query | 실행할 쿼리입니다. |
| cluster | 구성된  InfluxDB 클러스터의 이름입니다. 비어 있으면 기본 클러스터를 사용합니다. |

**************Example**************

`stream` 방법을 사용하여 기록 생성

```json
POST /kapacitor/v1/recordings/stream
{
    "task" : "TASK_ID",
    "stop" : "2006-01-02T15:04:05Z07:00"
}
```

`batch` 시작 시간을 지정하는 방법으로 기록 생성

```json
POST /kapacitor/v1/recordings/batch
{
    "task" : "TASK_ID",
    "start" : "2006-01-02T15:04:05Z07:00"
}
```

`stream`유형 `query`를 지정하여 기록 생성

```json
POST /kapacitor/v1/recordings/query
{
    "query" : "SELECT mean(usage_idle) FROM cpu WHERE time > now() - 1h GROUP BY time(10m)",
    "type" : "stream"
}
```

************응답************
