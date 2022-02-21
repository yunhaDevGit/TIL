
haproxy 네가지 필수 섹션

[https://www.haproxy.com/blog/the-four-essential-sections-of-an-haproxy-configuration/](https://www.haproxy.com/blog/the-four-essential-sections-of-an-haproxy-configuration/)

  

## 1.  Global
    

낮은 수준에서 HAProxy에 영향을 미치는 설정

  

1.  maxconn
    

-   HAProxy에 연결 가능한 최대 개수
    

2.  log
    

-   시작 또는 동작 중에 발생하는 에러나 이슈를 syslog에 기록한다.
    
-   request에 대한 정보도 기록한다
    

3.  user / group
    

-   user와 group은 HAProxy에게 초기화 후 권한을 삭제하도록 한다.
    
-   user와 group을 설정하지 않고 프로세스를 계속 동작시킬 경우, root 권한으로 계속 동작하기 때문에 user와 group을 미리 생성해야 한다.
    

4.  stats socket
    

-   서버 및 상태 확인을 동적으로 비활성화하고, 서버의 로드 밸런싱 가중치를 변경하고, 기타 유용한 레버를 당기는데 사용할 수 있는 런타임 API를 활성화 한다.
    

5.  nbproc / nbthread
    

-   HAProxy가 시작 시 생성해야 하는 프로세스 및 스레드 수를 지정
    
-   nbproc에 의해 생성된 프로세스는 각각 고유한 통계, stick tables, 상태 확인이 있다.
    
-   nbthread에 의해 생성된 스레드는 공유된다.
    

6.  ssl-default-bind-ciphers
    

-   모든 지시문에 기본적으로 SSL 및 TLS 암호를 열거한다.
    
-   지시문의 ciphers 매개변수를 추가하여 구체적인 설정을 할 수 있다.
    

7.  ssl-default-bind-options
    

-   ssl-min-ver 옵션은 이전 프로토콜에 대한 지원을 비활성화 하는 SSL/TLS 옵션이다.
    
-   예를 들어 TLS 버전 1.2 이상을 사용하는 연결만 수락하도록 선택할 수 있다.
    

  
  

## 2.  Defaults
    

중복 설정을 줄이는데 사용.

뒤에 있는 frontend 및 backend 섹션에 모두 적용된다.

defaults 설정은 하나로 제한되지 않고, defaults 작성 후에 또 다른 defaults 섹션을 적용할 경우 이전 섹션을 무시하고 모든 옵션을 기본값으로 재설정합니다.

  

1.  timeout connect / timeout client / timeout server
    

-   time connect - 백엔드 서버에 대한 TCP 연결이 설정될 때까지 HAProxy가 대기하는 시간을 구성
    
-   timeout client - 클라이언트가 말할 것으로 예상되는 기간, 즉 TCP 세그먼트를 보내는 기간 동안 비활성 상태를 측정
    
-   timeout server - 백엔드 서버가 요청할 것으로 예상될 경우 비활성을 측정
    
-   mode tcp, timeout server - HAProxy가 TCP 모드에서 켜질 경우, timeout client와 같아야 한다. HAProxy가 어디에 말해야 할 지 모르고 둘 다 항상 적용되기 때문에 다른 값을 사용하면 혼동을 일으킬 가능성이 높기 때문
    

2.  log global
    

-   각 후속 frontend에 전역 섹션에서 정의한 로그 설정을 사용하도록 지산하는 방법
    

3.  mode
    

-   HAProxy가 단순 TCP 프록시로 동작할지, 수신 트래픽의 상위 수준 HTTP 메시지를 검사할 수 있는지 정의한다.
    
-   frontend와 backend에서 동일하게 사용 시 defaults 섹션에 정의하는 것이 좋다.
    

4.  maxconn
    

-   frontend에서 연결 가능한 개수 제한을 설정하고 기본 값은 2000이다.
    

5.  option httplog
    

-   syslog에 메시지를 보낼 때 HAProxy에게 보다 자세한 로그 형식을 사용하도록 지정
    

  

## 3.  Frontend
    

HAProxy를 reverse proxy로 배치하면 frontend 섹션에서 클라이언트가 연결할 수 있는 IP 주소와 포트를 정의

1.  bind
    

-   IP 주소와 Port에 리스너를 할당
    
-   서버의 모든 IP주소에 바인딩 하기 위해 IP를 생략할 수 있으며, 포트는 단일 포트, 범위 또는 CSV로 구분지을 수 있다.
    

2.  http-request redirect
    

-   다른 URL을 시도해야 한다는 응답을 Client에게 보낸다.
    

3.  use_backend
    

-   주어진 조건이 true인 경우 수신 요청에 응답할 서버의 백엔드 풀을 선택한다.
    
-   haproxy 경로가 /api/로 시작하는 경우, 일부 기준에 따라 특정 백엔드를 선택할 수 있도록 하는 if path_beg/api/와 같은 ACL 문이 뒤에 나온다
    

4.  default_backend
    

-   거의 모든 frontend에서 찾을 수 있으며 use_backend 규칙이 트래픽을 다른 곳으로 먼저 보내지 않는 경우, 트래픽을 전송할 백엔드 이름을 제공한다.
    

  

## 4.  Backend  
    로드 밸런싱되고 요청을 처리하기 위해 할당될 서버 그룹 정의.
    

  

1.  balance
    

-   HAProxy가 요청에 응답할 서버를 선택하는 방법을 제어한다.
    
-   쿠키를 기반으로 특정 클라이언트를 항상 동일한 서버로 보낼 수 있다.
    
-   roundrobin - 가장 일반적인 로드 밸런싱 값으로 돌아가면서 서버를 선택한다.
    
-   leastconn - HAProxy가 활성 세션이 가장 적은 서버를 선택한다.
    

2.  cookie
    

-   cookie 설정은 쿠키 기반 지속성을 활성화 한다.
    
-   HAProxy에 SERVERUSED라는 쿠키를 클라이언트에 보내고 이를 초기 응답을 제공한 서버의 이름과 연결하도록 요청한다. 클라이언트는 세션 기간 동안 해당 서버와 계속 통신한다.
    

3.  option httpchk
    

-   HAProxy가 백엔드 서버에 레이어4(TCP) 대신 레이어7(HTTP)로 상태 확인 요청을 보낸다. 이때, 응답 하지 않는 서버에는 더이상 요청을 보내지 않는다.
    
-   기본적으로 HTTP 상태 확인은 OPTIONS를 사용하여 루트 경로(/)에 요청한다.
    
-   HAProxy는 2xx 또는 3xx 응답 코드를 성공적으로 처리하지만 `http-check` 를 통해 사용자 정의할 수 있다.
    

4.  default-server
    

-   상태 확인, 최대 접속량 등 `server`의 기본 값에 대해 설정한다.
    

5.  server
    

-   name - 첫 번째 인자, 서버 IP 주소와 포트 (IP 주소 대신 도메인명 작성 가능)
    
-   모든 server line에는 maxconn가 반드시 정의 되어 있어있다.
    

## 5.  Listen
    

frontend와 backend 섹션은 트래픽을 수신하여 서버 풀로 보낸다. listen 섹션을 사용하여 동일한 작업을 수행할 수 있다.

기본적으로 frontend와 backend 기능을 하나로 통합한다.

특히 많은 옵션과 함께 HTTP 모드를 사용할 때 frontend와 backend 섹션을 분리하며 간결한 구성을 선호하여 `listen` 접근 방식을 선호할 수 있다.

  

-   HTTP와 관련된 항목에 대해서 ‘listen’은 제거하는 것이 좋다.
    

실제로 처음에는 더 간단하지만, 애플리케이션에 구성 요소가 추가되기 시작하면 listen 섹션에 새로운 http-rules이 생길 것이다.

그럴 경우, frontend와 backend 부분에 특화 된 것을 구분하기 힘들기 때문에 분할하기 어렵다.

'listen'은 일반적으로 사용되는 순수 TCP 게이트웨이를 위해 사용된다.
