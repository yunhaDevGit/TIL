[https://github.com/influxdata/telegraf/blob/release-1.14/plugins/inputs/ping/README.md](https://github.com/influxdata/telegraf/blob/release-1.14/plugins/inputs/ping/README.md)

Ping 플러그인은 두 가지 주요 작동 방법이 있습니다 : `exec`와 `native`

시스템 호환성과 성능이 `native`가 더 뛰어나기 때문에 `native` 방식을 권장합니다. 하지만 이전 버전과의 호환성을 위해 `exec` 방법이 기본값 입니다.

`method="exec"`를 사용하는 경우, 시스템 ping utility가 실행되어 ping packet을 전송합니다.

대부분의 ping 명령 구현이 지원되며 현재는 GNU Inetutils ping에 대한 지원이 불가하여 `iputils-ping` 구현을 사용할 수 있습니다.

- **Inetutils ping과 iputils-ping 차이**
    
    `iputils-ping` 은 inetutils ping보다 더 많은 기능을 지원합니다. 
    
    `iputils-ping`은 `inetutils ping`에서 지원하는 모든 옵션을 지원합니다.
    
    추가로 `inetutils`는 Linux가 아닌 데비안 시스템에서 사용되는 GNU 구현입니다. 
    
    `iputils`는 Linux 전용이며 더 적은 수의 유틸리티를 포함합니다. 
    

```bash
$ apt-get install iputils-ping
```

![image](https://user-images.githubusercontent.com/74949294/200004408-3f2d4ce7-bc02-4d5f-9d72-069e9bb2a2c3.png)

`mehod = "native"`를 사용하면 Telegraf 프로세스에 의해 기본 Go로 결과가 전송되고 보고되므로 시스템 명령어 `ping` 을 실행할 필요 없습니다.

## 구성

```bash
[[inputs.ping]]
  ## Hosts to send ping packets to.
  urls = ["www.naver.com"]

  ## Method used for sending pings, can be either "exec" or "native".  When set
  ## to "exec" the systems ping command will be executed.  When set to "native"
  ## the plugin will send pings directly.
  ##
  ## While the default is "exec" for backwards compatibility, new deployments
  ## are encouraged to use the "native" method for improved compatibility and
  ## performance.
  # method = "exec"

  ## Number of ping packets to send per interval.  Corresponds to the "-c"
  ## option of the ping command.
  # count = 1

  ## Time to wait between sending ping packets in seconds.  Operates like the
  ## "-i" option of the ping command.
  # ping_interval = 1.0

  ## If set, the time to wait for a ping response in seconds.  Operates like
  ## the "-W" option of the ping command.
  # timeout = 1.0

  ## If set, the total ping deadline, in seconds.  Operates like the -w option
  ## of the ping command.
  # deadline = 10

  ## Interface or source address to send ping from.  Operates like the -I or -S
  ## option of the ping command.
  # interface = ""

  ## Specify the ping executable binary.
  # binary = "ping"

  ## Arguments for ping command. When arguments is not empty, the command from
  ## the binary option will be used and other options (ping_interval, timeout,
  ## etc) will be ignored.
  arguments = ["-c", "1", "-w", "2"]

  ## Use only IPv6 addresses when resolving a hostname.
  # ipv6 = false
```

![image](https://user-images.githubusercontent.com/74949294/200004491-712b6b31-8136-4ab1-a842-46063dc0cf77.png)

![image](https://user-images.githubusercontent.com/74949294/200004515-b7aa6783-2f4f-44a9-84d2-421452e23644.png)

ping 요청 후 응답 influxdb에 응답 결과 증적 여부 확인

![image](https://user-images.githubusercontent.com/74949294/200004376-cb9fc522-b9f5-4f1d-8872-cfc16f148b6b.png)

## 테스트

### 1. 인스턴스 생성 및 유동 IP 연결 (ping 테스트 target)

![image](https://user-images.githubusercontent.com/74949294/200004627-9fbd0026-c203-4b4b-97fe-cea571d335de.png)

![image](https://user-images.githubusercontent.com/74949294/200004647-b9d87fad-dfb2-48a7-8082-1c2916f3fe89.png)

### 2. 시스템 명령어를 통한 Ping 테스트

![image](https://user-images.githubusercontent.com/74949294/200004687-75a14319-8858-48af-8eb7-99edac63b88b.png)

![image](https://user-images.githubusercontent.com/74949294/200004720-ce5ab3b3-f45b-403d-8b7c-e587aafb8940.png)

-c count : 전송 되는 개수

-w timeout : -c와 함께 사용해야만 동작하는 옵션으로 최대 timeout 시간 동안 ping이 응답을 대기합니다.

![image](https://user-images.githubusercontent.com/74949294/200004737-eaa64de7-d7eb-4f49-8403-93928a11934c.png)

![image](https://user-images.githubusercontent.com/74949294/200004770-74c01acf-9aa7-4ac3-9057-807cb69624b9.png)

### 3. telegraf.conf 변경 및 재시작

```bash
[[inputs.ping]]
  urls = ["ping target address"]
  count =1
  ping_interval = 5.0
  timeout = 2.0
  deadline = 2
```

![image](https://user-images.githubusercontent.com/74949294/200005005-c8486c8c-a5eb-41bb-a6d6-2a23ba4ba6c6.png)

count (-c 옵션) : 간격당 보낼 ping 패킷 수

ping_interval (-i 옵션) : ping 패킷을 보내는 사이의 대기 시간(초)

timeout (-W 옵션) : ping 응답을 기다리는 시간(초)

deadling (-w 옵션) : ping 종료 시간(초)

![image](https://user-images.githubusercontent.com/74949294/200004932-5ec28697-bbe4-4b89-aabb-a92076a9b625.png)

아래와 같이 influxdb에 vm 경로가 추가된 것을 확인할 수 있습니다.

![image](https://user-images.githubusercontent.com/74949294/200004895-1eb6cbc3-d029-4f7f-975a-40fe7facf09f.png)

### 4. 인스턴스 전원 off 후 telegraf ping 결과

![image](https://user-images.githubusercontent.com/74949294/200004855-0cf85e77-d7c5-4b0f-a9db-f1c833b20fe5.png)

![image](https://user-images.githubusercontent.com/74949294/200004817-e03f7f39-f03f-4b16-868a-4c42e21d488c.png)

`pervent_packet_loss` 항목이 급격히 증가하는 것을 확인할 수 있습니다.
