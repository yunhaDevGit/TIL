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

​       https://rjswn0315.tistory.com/37