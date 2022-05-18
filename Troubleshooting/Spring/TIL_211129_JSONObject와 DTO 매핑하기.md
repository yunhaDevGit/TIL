Spring Boot Project를 하면서 json 형태의 string을 한 모듈에서 또 다른 모듈에서 전달하였다.

전달 받은 string을 JSONObject를 사용하여 json 형태로 변환해주었다. 

JSONObject를 사용하기 위해 다음의 dependency를 추가해줘야 한다.

**build.gradle**

```java
implementation 'org.json:json:20210307'
```

**Java Class**

```java
String action = "{"actionId":"vm_create actionId","object":{"id":"9ffdee90-7711-45fc-aaa3-37e72925d4f3","name":"test_vm","cpuNum":3,"memSize":4,"vncPort":3},"actionCode":"VM_CREATE"}"

JSONObject actionObject = new JSONObject(action); // json으로 변환
JSONObject jsonObject = actionObject.optJSONObject("object"); // actionObject 내부 value `object`에 접근
```

**JSONObject**는 java에서 String으로 되어 있는 json을 json 형태로 변환해주는 클래스이다.

**optJSONObject**를 사용하여 json 내부 key의 value들을 파싱할 수 있다. 

이렇게 얻은 jsonObject와 VmDto와 매핑 하기 위해 아래와 같이 작성해주었다.

```java
VmDto vmDto = (VmDto) jsonObject;
```

위와 같이 구현하니 다음과 같은 에러 메시지와 함께 build가 되지 않았다. 

> Inconvertible types; cannot cast 'org.json.JSONObject' to 'io.kafka.cloud.kafkacommon.dto.VmDto'

JSONObject는 DTO class와 바로 매핑되지 않나보다.

찾아보니 JSONObject를 DTO에 매핑 하기 위해서는 GSON이라는 것을 사용해야 한다고 한다. 

**build.gradle**

```java
implementation 'com.google.code.gson:gson:2.8.6'
```

GSON을 사용하기 위해 라이브러리 의존성을 추가해준다.

### JSONObject를 DTO에 매핑하기

- JsonObject는 Map과 마찬가지로 {key:value}의 형식을 가지고 있는데, **gson은 해당 key/value를 dto에 선언한 변수와 자동 매핑 해주는 것을 지원해준다.**

```java
Gson gson = new Gson();
VmDto vmDto = gson.fromJson(jsonObject.toString(), VmDto.class);
```

이런 식으로 사용되며 Key가 일치하는 것만 매핑 되어 값이 삽입된다. 

[https://mine-it-record.tistory.com/257](https://mine-it-record.tistory.com/257)
