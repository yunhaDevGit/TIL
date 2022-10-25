# Kapacitor API

## 기본 정보

- Kapaciator는 HTTP API를  ******9092****** 포트로 제공합니다.
- 모든 요청은 `/kapacitor/v1`와 같이 version과 namespace을 기본 경로로 사용합니다.

### HTTP 응답 코드

| 응답 코드 | 설명 |
| --- | --- |
| 2xx | 요청이 성공했으며 요청에 따라 콘텐츠는 다릅니다. |
| 4xx | 잘못된 요청입니다. 요청에 문제가 있는 경우 오류를 참조하세요. 요청을 반복하면 오류가 계속 반환 됩니다. |
| 5xx | 서버가 요청을 처리할 수 없습니다. 이유에 대한 오류를 참조하십시오. 서버 문제가 해결 된 경우 요청을 반복하면 성공할 수 있습니다.  |

### 에러

모든 요청은 실패한 요청에 대한 추가 정보 제공을 위해 아래의 형식으로 JSON을 반환합니다. 

```json
{ "error" : "error message" }
```

### Query parameters vs JSON body

쿼리 매개변수는 GET 요청에서만 사용됩니다. 다른 모든 요청에는 JSON 본문의 매개변수가 필요합니다. 

> `/kapacitor/v1/write` 는 예외 규칙으로 `/write` endpoint는 Kapacitor가 InfluxDB와 호환 되도록 합니다.
> 

### Links

리소스를 생성할 때 Kapacitor API 서버는 `href` 리소스의 `link` object를 반환합니다. 클라이언트는 경로 조작 없이 이전 호출에서 제공된 링크를 사용할 수 있어야 합니다.

### IDs

ID 제어 할 수 있도록 Kapacitor API를 사용하면 클라이언트가 다양한 리소스에 대한 ID를 지정할 수 있습니다. ID를 지정하지 않으면 Kapacitor는 리소스에 대한 임의의 UUID를 생성합니다. 

모든 ID는 아래의 정규식과 일치해야 합니다. 

```
^[-\._\p{L}0-9]+$`
```

## 데이터 쓰기

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

## 작업 관리

작업은 Kapacitor가 수행할 작업을 나타냅니다. task는 id, type, TICKscript, 데이터베이스 유지 정책 등으로 정의 됩니다.

****************************************************************task 정의 및 업데이트****************************************************************

task 정의하기 위해서는 `/kapacitor/v1/tasks` endpoint로 POST 요청을 하면 됩니다. 만약 task가 이미 있다면 `PATCH` 메서드를 통해 task 속성을 수정하면 됩니다.

아래의 옵션을 JSON 객체에 담아 정의할 수 있습니다. 

| Property | Puropose |
| --- | --- |
| id | task의 식별자. 만약 빈 값의 경우 랜덤 ID |
| template-id | TICKscript와 type을 지정하는 대신 사용할 템플릿 ID |
| type | task 타입: stream, batch |
| dbrps |  |
| script |  |
| status |  |
| vars |  |
