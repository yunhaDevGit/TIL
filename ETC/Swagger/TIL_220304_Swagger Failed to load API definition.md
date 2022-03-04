## 문제

단일 모듈 환경에서의 Swagger UI 정상 동작 여부를 확인 후, 멀티 모듈로 변환 후 Swagger UI를 실행 했을 경우 아래와 같은 에러가 발생했습니다.
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FIMvIW%2Fbtru5rtu6Zo%2FF2wKFCFi9A0uGh2lKKeRb1%2Fimg.png)

```
Ambiguous handler methods mapped for '/v3/api-docs'
```

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F4KWsJ%2Fbtru5bjB9I9%2FziIak9GVWdNjfo62d5iKBK%2Fimg.png)


## 해결

JSON 형식의 OpenAPI의 문서 경로가 중복되어 발생하는 문제라고 판단하여 application.yml 에 아래와 같은 설정을 추가해주었습니다.

```
springdoc:
  api-docs:
    path: /api-docs/network
```

해당 설정 추가 후 아래와 같이 Swagger UI가 정상적으로 보입니다.

OpenAPI의 api-docs의 기본 URL이 /v3/api-docs 여서 발생한 에러였습니다.
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FrDApd%2Fbtru2wV7bAL%2FoPSuKVIB962XTEzb0Pul70%2Fimg.png)
