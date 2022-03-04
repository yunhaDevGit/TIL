## 문제

단일 모듈 환경에서의 Swagger UI 정상 동작 여부를 확인 후, 멀티 모듈로 변환 후 Swagger UI를 실행 했을 경우 아래와 같은 에러가 발생했습니다.

[##_Image|kage@IMvIW/btru5rtu6Zo/F2wKFCFi9A0uGh2lKKeRb1/img.png|CDM|1.3|{"originWidth":936,"originHeight":470,"style":"alignCenter","width":637,"height":320}_##]

```
Ambiguous handler methods mapped for '/v3/api-docs'
```

[##_Image|kage@4KWsJ/btru5bjB9I9/ziIak9GVWdNjfo62d5iKBK/img.png|CDM|1.3|{"originWidth":1280,"originHeight":257,"style":"alignCenter","width":790,"height":159}_##]

## 해결

JSON 형식의 OpenAPI의 문서 경로가 중복되어 발생하는 문제라고 판단하여 application.yml 에 아래와 같은 설정을 추가해주었습니다.

```
springdoc:
  api-docs:
    path: /api-docs/network
```

해당 설정 추가 후 아래와 같이 Swagger UI가 정상적으로 보입니다.

OpenAPI의 api-docs의 기본 URL이 /v3/api-docs 여서 발생한 에러였습니다.

[##_Image|kage@rDApd/btru2wV7bAL/oPSuKVIB962XTEzb0Pul70/img.png|CDM|1.3|{"originWidth":917,"originHeight":536,"style":"alignCenter","width":759,"height":444}_##]
