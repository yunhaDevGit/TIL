# Post를 이용한 장애 알림 테스트

### 1. Alert Rules 생성

![image](https://user-images.githubusercontent.com/74949294/198267692-c8cce0b7-7904-4b18-a3b8-4e6d75a41299.png)

### 2. Alert 조건 설정

테스트를 위해 CPU usage_idle이 80% 미만일 경우 알람이 갈 수 있도록 설정하였습니다. 

![image](https://user-images.githubusercontent.com/74949294/198267710-e7e2e384-9ba2-4576-9d1f-a35042204d57.png)

알람 타입은 아래와 같습니다.

![image](https://user-images.githubusercontent.com/74949294/198267729-b3463537-5545-4a9f-96cf-76fe2e242b03.png)

![image](https://user-images.githubusercontent.com/74949294/198267743-ab344f9e-1078-424a-9f84-a460e62fd333.png)

### 3. 테스트 Controller 생성

**************************************TestController.java**************************************

Kapacitor로부터 `post`응답을 받기 위해 아래와 같이 테스트 Controller를 작성하였습니다. 

![image](https://user-images.githubusercontent.com/74949294/198267766-ab13969e-21e7-4d7a-80e2-c101c9007e03.png)

추가로 WebSecurityConfig에 아래와 같이 설정하여 해당 POST 요청을 허용해주도록 하였습니다.

![image](https://user-images.githubusercontent.com/74949294/198267798-8b22fc6e-39f1-4214-8815-6761692f831c.png)

### 4. EndPoint 지정

해당 alert 발생 시 아래의 HTTP URL로 요청을 보낼 수 있도록 EndPoint를 지정하였습니다. 

![image](https://user-images.githubusercontent.com/74949294/198267816-562b3de7-074c-4113-9458-6f5971967b16.png)

### 5. 테스트

**************************************CPU 부하 테스트**************************************

CPU 부하 테스트를 위한 stress 설치

```bash
$ sudo yum install -y epel-release
$ sudo yum install -y stress
```

stress 명령

```bash
$ stress -c {코어 수}
```

![image](https://user-images.githubusercontent.com/74949294/198267839-07d9bf51-33ae-4fee-ac0f-aa21e3bd575c.png)

********응답********

```json
{
    "id": "CPU Rule",
    "message": " CPU Rule is  CRITICAL for  18.245614034723264",
    "details": "{\u0026#34;Name\u0026#34;:\u0026#34;cpu\u0026#34;,\u0026#34;TaskName\u0026#34;:\u0026#34;chronograf-v1-957bf549-2cf0-4946-acb9-c0431c78cb27\u0026#34;,\u0026#34;Group\u0026#34;:\u0026#34;nil\u0026#34;,\u0026#34;Tags\u0026#34;:{\u0026#34;cpu\u0026#34;:\u0026#34;cpu0\u0026#34;,\u0026#34;host\u0026#34;:\u0026#34;cmp-influxdb-research.novalocal\u0026#34;,\u0026#34;target\u0026#34;:\u0026#34;vm\u0026#34;,\u0026#34;uuid\u0026#34;:\u0026#34;105ddc6b-b002-4a91-aec0-efe0df746bd9\u0026#34;},\u0026#34;ServerInfo\u0026#34;:{\u0026#34;Hostname\u0026#34;:\u0026#34;localhost\u0026#34;,\u0026#34;ClusterID\u0026#34;:\u0026#34;6c8875b4-c8ac-4ff7-9493-4e5387ab72ef\u0026#34;,\u0026#34;ServerID\u0026#34;:\u0026#34;31f4c3eb-f6a0-4e16-bc73-7a70a547db00\u0026#34;},\u0026#34;ID\u0026#34;:\u0026#34;CPU Rule\u0026#34;,\u0026#34;Fields\u0026#34;:{\u0026#34;value\u0026#34;:18.245614034723264},\u0026#34;Level\u0026#34;:\u0026#34;CRITICAL\u0026#34;,\u0026#34;Time\u0026#34;:\u0026#34;2022-10-21T02:01:20Z\u0026#34;,\u0026#34;Duration\u0026#34;:0,\u0026#34;Message\u0026#34;:\u0026#34; CPU Rule is  CRITICAL for  18.245614034723264\u0026#34;}\n",
    "time": "2022-10-21T02:01:20Z",
    "duration": 0,
    "level": "CRITICAL",
    "data": {
        "series": [
            {
                "name": "cpu",
                "tags": {
                    "cpu": "cpu0",
                    "host": "cmp-influxdb-research.novalocal",
                    "target": "vm",
                    "uuid": "105ddc6b-b002-4a91-aec0-efe0df746bd9"
                },
                "columns": [
                    "time",
                    "value"
                ],
                "values": [
                    [
                        "2022-10-21T02:01:20Z",
                        18.245614034723264
                    ]
                ]
            }
        ]
    },
    "previousLevel": "OK",
    "recoverable": true
}
```

## TICKscript
```
var db = 'klid'
var rp = 'autogen'
var measurement = 'cpu'
var groupBy = []
var whereFilter = lambda: isPresent("usage_idle")
var name = 'CPU Rule'
var idVar = name
var message = ' {{.ID}} is  {{.Level}} for  {{ index .Fields "value" }}'
var idTag = 'alertID'
var levelTag = 'level'
var messageField = 'message'
var durationField = 'duration'
var outputDB = 'chronograf'
var outputRP = 'autogen'
var outputMeasurement = 'alerts'
var triggerType = 'threshold'
var details = 'CPU usage_idle이 80% 미만입니다.'
var crit = 80

var data = stream
    |from()
        .database(db)
        .retentionPolicy(rp)
        .measurement(measurement)
        .groupBy(groupBy)
        .where(whereFilter)
    |eval(lambda: "usage_idle")
        .as('value')

var trigger = data
    |alert()
        .crit(lambda: "value" < crit)
        .message(message)
        .id(idVar)
        .idTag(idTag)
        .levelTag(levelTag)
        .messageField(messageField)
        .durationField(durationField)
        .details(details)
        .stateChangesOnly()
        .post('http://192.168.130.26:8080/api/cmp/test')
        .email()

trigger
    |eval(lambda: float("value"))
        .as('value')
        .keep()
    |influxDBOut()
        .create()
        .database(outputDB)
        .retentionPolicy(outputRP)
        .measurement(outputMeasurement)
        .tag('alertName', name)
        .tag('triggerType', triggerType)

trigger
    |httpOut('output')
```
