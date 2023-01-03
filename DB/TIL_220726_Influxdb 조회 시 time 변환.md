# [InfluxDB] InfluxDB time 보기 편하게 변환

터미널에서 influxdb에 접속하여 데이터를 조회할 경우 time이 아래와 같이 `UnixTimestamp`로 조회 되어 해당 시간이 몇 시 몇 분인지 확인하기가 힘듭니다. 

![image](https://user-images.githubusercontent.com/74949294/180956814-9ea946d2-3ed0-42c1-9ced-784026aca6eb.png)

우리가 알아보기 쉽게 time을 출력하는 방법을 알아보겠습니다.

### 1. precision rfc3339 명령어

아래의 명령어를 입력하면 알아보기 쉬운 UTC 시간으로 보이는 것을 확인 할 수 있습니다. 

```powershell
> precision rfc3339
```

![image](https://user-images.githubusercontent.com/74949294/180956837-86f89a49-f3f0-4a01-a9f9-f4d08a79c70b.png)

### 2. Influxdb 접속 시 -precision rfc3339

influxdb 처음 접속 시 해당 명령어를 함께 작성하면 위와 동일한 결과를 얻을 수 있습니다. 

![image](https://user-images.githubusercontent.com/74949294/180956870-0988791a-c47d-49f6-8c7e-c229dd9e8ecd.png)

만약 시간이 LocalTime과 맡지 않을 경우, UTC로 설정되어 있어 9시가 차이가 날 것입니다. 

이럴 경우, select문 뒤에 `tz('Asia/Seoul')`을 붙여주면 제대로 된 시간이 보일 것 입니다.

![image](https://user-images.githubusercontent.com/74949294/180956897-52e705f0-8209-4c69-8e27-57254973aa86.png)
