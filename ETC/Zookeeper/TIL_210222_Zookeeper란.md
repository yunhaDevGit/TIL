# Zookeeper

### zookeeper란?

- 분산 코디네이터(분산 처리 환경에서 사용 가능한 데이터 저장소)이다.

- zookeeper는 디렉토리 구조로 데이터 저장을 하고, 데이터가 변경되면 클라이언트에게 어떤 노드가 변경됐는지 콜백을 통해 알려준다.

- zookeeper namespace의 모든 노드는 경로로 식별된다.  (ex, ls /zookeeper/queue...)

- 모든 znode는 데이터 모델을 관리하기 위한 메타데이터를 가진다.
- 메타 데이터는 버전 넘버(데이터 업데이트 될 때마다 버전 넘버가 업데이트 된다), ACI(Action Control List- znode에 접근하기 위한 권한 획득 메커니즘), 타임 스탬프(znode 생성되고 난 후의 경과 시간 및 업데이트 시간), 데이터 길이로 구성된다.
- 어플리케이션들은 각각 클라이언트가 되어 zookeeper 서버들과 커넥션을 맺은 후 zookeeper의 znode에 key-value 형태로 저장된 상태 정보등을 주고 받는다.

https://engkimbs.tistory.com/660



### 동작

<img src="https://lh3.googleusercontent.com/YMDbUbiFaNv2Vtvvm45RjTGoxyiJGtq8gyh0RpUW5_DDU0VfCtKtXdsFVLT8gSThPEhNyAHF2sAcX9zRq8Le7_QR3kXSNZ7kkINByQIv8TwAsVIYYHfj27XFXRf_HN4-EmrgdUbS" alt="img" style="zoom:80%;" />

1. 클라이언트가 특정 서버에 접속하여 그 서버에 데이터를 업데이트한다
2. 업데이트 된 서버는 leader 서버에 데이터를 알리고 업데이트한다.
3. leader 서버는 그 정보를 다른 곳에 브로드 캐스트한다.
4. 업데이트 정보를 받은 나머지 follower 주키퍼 서버들은 그 내용을 갱신하여 전체 서버들의 데이터들이 일관된 상태로 유지된 상태로 있는다.

![img](https://lh6.googleusercontent.com/uGLJmgCknhwt5Y7g7Dubb9PdB0ICn466eoQG76V4BoKwQ_vmyN6Qrk9w1nBQtv_QA1G-hF_shplAMhC86lIcTVTBEDA1knET0EowWEKYQKi-GhoOhXXtUgeTP-JXwoLP-m0vBRLm)

- Leader
- Follower



**클라이언트에서 zookeeper 앙상블(zookeeper cluster)에 연결할 때 커넥션 문자열에 앙상블을 구성하는 zookeeper 서버 주소를 다수 포함할 수 있다.**

**여러 개의 zookeeper 서버를 두고 하나의 클러스터로 동작. 클라이언트를 여러 서버에 분산**

- zookeeper 서버 중 하나가 죽어도 주키퍼 자체에는 영향이 없도록 지속적으로 동작해야 한다.
  - 데이터를 복제해서 다른 zookeeper 서버에도 동일하게 저장한다
  - 모든 zookeeper 서버의 데이터가 동기화 되기 위해 리더를 뽑는다

http://blog.naver.com/PostView.nhn?blogId=alice_k106&logNo=220614228476



1. 클라이언트는 커넥션 문자열에 포함 된 zookeeper 주소 중 하나에 접근해서 세션을 맺는다

2. 1) 읽기 명령 : 클라이언트가 전달한 읽기 명령은 현재 연결된 zookeeper 서버에서 바로 반환

   2) 쓰기 명령 : 리더 서버로 전달되고, 리더 서버는 팔로워 서버들에게 해당 쓰기를 할 수 있는지 질의 후, 과반수 이상의 follower에게 쓸 수 있다는 응답을 받으면 리더는 follower에게 데이터를 쓰도록 지시한다.



------

##### cloudit zookeeper queue

replication

- topic으로 통하는 모든 데이터의 read/write는 오직 leader에서 이뤄지고, follower는 leader와 sync를 유지함으로써 leader에 문제가 생겼을 경우 follower들 중 하나가 leader 역할을 한다.



### Curator / CuratorFramework

##### curator

- Java/JVM client library for Apache Zookeeper
- Apache zookeeper를 더 쉽고 안정적으로 사용할 수 있는 고급 API 프레임워크와 유틸리티 제공

##### CuratorFramework

- zookeeper 사용을 훨씬 단순화 하는 고급 API
- zookeeper를 기반으로 하는 많은 기능을 제공(자동 커넥션 관리, 쉬운 API 제공 등) 
- zookeeper 클러스터 당 하나의 curatorFramework를 사용해야 한다.

​    https://curator.apache.org/curator-framework/index.html



### Znode

- zookeeper에 저장되는 node

  ex) ./zkCli.sh  ->  ls /cloudit(znode)

- tree 형태이다(리눅스 파일 시스템과 유사한 형태)

  <img src="https://lh5.googleusercontent.com/r4lyJ8ToZP5_hmL1QSfUc2K4TBBGcOpxAbPgMy7c7onFT47nIIUkYa1yFnTLpTuxIRynXAvKs9VfUCP3oSZ6ly7DEmPilvSvDWDf6fdMl6_Y3vY-fgmp3hGB7DNhI72hMPwm4K2w" alt="img" style="zoom:150%;" />





### Watch

- watch 기능은 zookeeper 클라이언트가 특정 znode에 watch를 설정해 놓아야 한다.

  ![img](https://lh3.googleusercontent.com/EqRfvFN9DmgKn7nDec0bVU4D3KaMHYc6Q9NHU9C5jMPpn2b5_NHtAcuiOozyXxGhR0Qgg1J2YhmOgFkZPnKz7NST8jVR9EEEdgn_3vL_Bj2LTH9dl_gN73wfmEDlAfG3Mm1p-aud)

- 해당 znode가 변경되면 watch를 설정한 클라이언트에게 event를 전달 후 해당 watcher는 삭제된다.

- watch가 트리거되면 클라이언트는 znode가 변경됨을 알리는 패킷을 보낸다.

- watch란 모니터링 기능과 다르지 않다.

​       https://rjswn0315.tistory.com/37