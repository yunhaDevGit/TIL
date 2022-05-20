# Clustering Guide

- **How RabbitMQ nodes are identified: [node names](https://www.rabbitmq.com/clustering.html#node-names)**
- **[Requirements](https://www.rabbitmq.com/clustering.html#cluster-formation-requirements) for clusteringWhat data is and isn't [replicated between cluster nodes](https://www.rabbitmq.com/clustering.html#cluster-membership)**
- **What clustering [means for clients](https://www.rabbitmq.com/clustering.html#clustering-and-clients)**
- **[How clusters are formed](https://www.rabbitmq.com/clustering.html#cluster-formation)**
- **How nodes [authenticate to each other](https://www.rabbitmq.com/clustering.html#erlang-cookie) (and with CLI tools)**
- **Why it's important to [use an odd number of nodes](https://www.rabbitmq.com/clustering.html#node-count) and two cluster nodes are highly recommended against**
- **[Node restarts](https://www.rabbitmq.com/clustering.html#restarting) and how nodes rejoin their cluster**
- **[Node readiness probes](https://www.rabbitmq.com/clustering.html#restarting-readiness-probes) and how they can affect rolling cluster restarts**
- **How to [remove a cluster node](https://www.rabbitmq.com/clustering.html#removing-nodes)**
- **How to [reset a cluster node](https://www.rabbitmq.com/clustering.html#resetting-nodes) to a pristine (blank) state**

### Port Access

rabbitmq 노드들은 클라이언트와 연결하기 위해 port를 사용합니다.

- 4369: Erlang 노드 포트 매핑 데몬 포트
- 25672: rabbitmq 노드들의 통신을 위해 사용되는 Erlang 노드 포트
- 5671, 5672: AMQP 클라이언트 포트
- 15672: rabbitmq_management 플러그인 포트
- 35672-35682: used by CLI tools (Erlang distribution client ports) for communication with nodes and is allocated from a dynamic range (computed as server distribution port + 10000 through server distribution port + 10010). See networking guide for details.
- 61613, 61614: STOMP clients without and with TLS (only if the STOMP plugin is enabled)
- 1883, 8883: (MQTT clients without and with TLS, if the MQTT plugin is enabled
- 15674: STOMP-over-WebSockets clients (only if the Web STOMP plugin is enabled)
- 15675: MQTT-over-WebSockets clients (only if the Web MQTT plugin is enabled)
- 15692: Prometheus metrics (only if the Prometheus plugin is enabled)

## Node Names

RabbitMQ 노드는 노드 이름으로 구분됩니다. 노드 이름은 prefix(보통 `rabbit`)과 호스트 이름으로 구분됩니다. 예를 들어  `rabbit@node1.messaging.svc.local` 일 경우 `rabbit`은 prefix, `node1.messaging.svc.local`은 호스트명입니다.

노드 이름은 클러스터 내에서 유일해야 합니다. 만약 하나의 호스트에서 두 개 이상의 노드가 동작 할 경우  prefix를 다르게 해야 합니다. ( `rabbit1@hostname`, `rabbit2@hostname` )

클러스터 내에서 노드는 노드 이름을 사용하여 서로를 식별합니다. 노드가 시작될 때 노드 이름이 할당되었는지 확인합니다. `RABBITMQ_NODENAME` 이라는 환경 변수를 통해 이뤄집니다. 명시적으로 구성된 값이 없는 경우 노드는 호스트 이름을 확인하고 노드 이름을 계산하기 위해 여기에 `rabbit`을 추가합니다.

## Hostname 정의

RabbitMQ 노드는 짧은 도메인 또는 정규화된(FQDN) 도메인 이름을 사용하여 서로 주소를 지정합니다. 따라서 모든 클러스터 구성원의 호스트 이름은 `Rabbitmqctl` 과 같은 커맨드 도구가 사용될 수 있는 시스템 뿐만 아니라 모든 클러스터 노드에서 확인할 수 있어야 합니다 .

호스트명은 OS 에서 제공하는 방법 중 하나로 확인할 수 있습니다.

- DNS records
- Local host files (ex, `/etc/hosts`)

제한적인 환경에서는 DNS record나 호스트 파일 수정이 제한되거나 불가능할 경우 Erlang VM을 구성하여 호스트 이름 확인을 할 수 있습니다. 

## Nodes in a Cluster

## What is Replicated

**RabbitMQ Cluster를 구성하는 RabbitMQ는 Queue를 제외한 모든 정보를 공유한다는 특징을 가지고 있습니다.** 따라서 동일 Cluster 안에 있는 모든 RabbitMQ는 동일한 Exchange를 가지고 있습니다. 또한 **RabbitMQ Cluster에서 기본적으로 Queue는 한 노드에만 존재한다는 특징도 갖습니다.** 

클러스터의 여러 노드에 `queue`를 복제하려면 복제를 지원하는 `queue` 유형을 사용합니다. 해당 내용은 `Quorum Queue`에서 확인할 수 있습니다. 

## Nodes are Equal Peers

여러 분산 시스템에서는 리더와 팔로워 노드가 있는데 RabbitMQ에서는 일반적으로 맞지 않습니다. 클러스터 내의 RabbitMQ의 모든 노드는 동일합니다 (RabbitMQ core에서는 특별한 노드가 없습니다). 이 항목은 Quorum queue 및 플러그인을 고려했을 때 차이가 있지만 대부분의 경우 모든 클러스터 노드가 동일할 것으로 간주되어야 합니다.

모든 노드에 대해 많은 CLI 도구 작업을 실행할 수 있습니다. HTTP API 클라이언트는 모든 클러스터 노드를 대상으로 할 수 있습니다.

개별 플러그인은 특정 노드를 일정 기간 동안 "특별한" 노드로 지정할 수 있습니다. 예를 들어 연합 링크는 특정 클러스터 노드에 있습니다. 해당 노드에 오류가 발생하면 다른 노드에서 링크가 다시 시작됩니다.

 

## How CLI Tools Authenticate to Nodes (and Nodes to Each Other) : the Erlang Cookie

RabbitMQ 노드와 CLI 도구(`rabbitmqctl`)는 다른 노드와 통신하기 위해 cookie를 사용합니다. 두 노드 간의 연결을 하기 위해 `Erlang cookie`라고 부르는 동일한 secret 값을 가지고 있어야 합니다. cookie는 문자와 숫자가 섞인 255자 이상의 문자열이고, 보통 local file에 저장되어 있습니다. 해당 파일은 소유자만이 접근할 수 있고, 모든 클러스터 노드는 동일한 쿠키를 가지고 있습니다. 

파일이 없을 경우, Erlang VM은 RabbitMQ가 시작될 때 랜덤으로 해당 값을 만드는 것을 시도합니다. 이렇게 생성 된 쿠키는 개발 환경에서만 사용해야 합니다. 

Erlang 쿠키 생성은 클러스터 배포 단계에서 수행되어야 하며, 이상적으로는 자동화 및 조정 도구를 사용해야 합니다.

### Linux, MacOS, BSD

Cookie 파일은 Unix 시스템에서 `/var/lib/rabbitmq/.erlang.cookie`나 `$HOME/.erlang.cookie`에 위치합니다. 

### Community Docker Image

[Docker Image](https://github.com/docker-library/rabbitmq/)는 `RABBITMQ_ERLANG_COOKIE` 환경 변수 값을 사용하여 쿠키 파일을 채웁니다.

이미지를 사용하는 구성 관리 및 컨테이너 오케스트레이션 도구는 클러스터 내의 모든 RabbitMQ 노드가 동일한 값을 사용하는지 확인해야 합니다.

### Troubleshooting

log 파일 경로

```powershell
node : rabbit@cdbf4de5f22d
home dir : /var/lib/rabbitmq
```

**Authentication Failures**

cookie가 잘못 구성될 경우 RabbitMQ 노드는 `Connectio attempt from disallowed node`, `Could not auto-cluster`와 같은 에러 로그를 출력합니다.

### Node Counts and Quorum

Quorum Queue, MQTT 클라이언트 추적과 같은 몇몇 기능이 클러스터 구성원 간의 연결이 필요하기 때문에 클러스터 노드를 홀수 개로 사용하는 것이 좋습니다 (3,5,7)

두 노드로 구성된 클러스터는 클러스터 노드가 과반수를 식별하고 연결이 끊겼을 경우 다시 합의하는 것이 불가능하기 때문에 권장하지 않습니다.

4개나 6개로 구성된 클러스터는 노드간 합의의 관점에서 봤을 때 3개나 5개의 노드로 구성된 클러스터와 성능이 동일합니다. 

### Clustering and Clients

모든 클러스터 멤버가 사용 가능하고, 클라이언트는 모든 노드에 연결되고 모든 작업을 수행할 수 있다고 가정해보겠습니다. 노드는 quorum queue의 리더나 queue leader replica에 작업을 요청할 것입니다. 

지원되는 모든 메세징 프로토콜은 클라이언트가 한 번에 하나의 노드와만 연결될 수 있도록 합니다. 

만약 노드가 실패할 경우, 클라이언트는 토폴로지를 복구하고 계속해서 운영하기 위해 다른 노드에 재연결을 시도합니다. 이러한 이유로 대부분의 클라이언트 라이브러리는 엔드포인트 목록(호스트 이름 또는 IP 주소)을 연결 옵션으로 허용합니다. 호스트 목록은 클라이언트가 지원하는 경우 초기 연결 및 연결 복구 중에 사용됩니다.

Quorum Queue를 사용할 경우 클라이언트는 온라인에서 복제된 quorum이 있는 큐에서만 작업할 수 있습니다.

### Clustering and Observability

클라이언트 연결, 채널 및 queue는 클러스터 노드들에 분산됩니다. 운영자는 모든 클러스터 노드에서 리소스 검사 및 모니터링을 할 수 있어야 합니다.

`rabbitmq-diagnostics`와 `rabbitmqctl`과 같은 RabbitMQ CLI 도구는리소스 및 클러스터 전체 상태를 확인하는 명령을 제공합니다. (`rabbitmqctl list_connections`, `rabbitmqctl list_mqtt_connection`, `rabbitmqctl_list_users`)

이러한 “클러스터 전체” 명령은 한 노드에 먼저 연결한 후 클러스터 멤버를 검색하여 모든 노드에 연결하고 각각의 상태를 검색하고 결합합니다. 

### Node Failure Handling

RabbitMQ 브로커는 개별 노드의 실패를 허용합니다. 종료 시점에 알려진 클러스터 멤버 노드에 연결할 수 있으면 노드를 마음대로 시작하고 중지할 수 있습니다.

Quorum queue는 병렬 복제와 예측 가능한 리더 선택 그리고 data safety를 통해 여러 클러스터 노드에 큐를 복제할 수 있습니다.

### Metrics and Statistics

모든 노드는 자체 메트릭과 통계를 저장하고 집계하며 다른 노드가 접근할 수 있도록 API를 제공합니다. 일부 통계는 클러스터 전체에 적용되고 나머지는 개별 노드에만 적용됩니다. 

HTTP API 요청에 응답하는 노드는 데이터를 검색하고 집계한 결과를 peer에게 응답합니다.

### Clustering Transcript with `rabbitmqtl`

사용자가 세 대의 RabbitMQ에 로그인 되어 있고, 사용자 PATH에 rabbitmq-server 및 rabbitmqctl script가 설치되어 있다고 가정하겠습니다. 

이 스크립트는 단일 호스트에서 실행되도록 수정할 수 있습니다. 

### Starting Independent Nodes

클러스터는 기존 RabbitMQ 노드를 클러스터 구성으로 재구성하여 설정됩니다. 

따라서 첫 번째 단계는 일반적인 방법으로 모든 노드에서 RabbitMQ를 실행합니다. 

```powershell
# on rabbit1
$ rabbitmq-server -detached

# on rabbit2
$ rabbitmq-server -detached

# on rabbit3
$ rabbitmq-server -detached
```

cluster_status 명령을 통해 각 노드에 하나씩 독립된 RabbitMQ 브로커가 생성된 것을 확인할 수 있습니다. 

```powershell
$ rabbitmqctl cluster_status
```

### Creating a Cluster

클러스터에서 세 개의 노드를 연결하기 위해 두 노드(`rabbit@rabbit2`, `rabbit@rabbit3`) 에게 `rabbit@rabbit1` 클러스터에 합류하도록 지시합니다. 그 전에 연결 될 두 노드를 `reset` 해야 합니다.

`rabbit@rabbit2` 를 `rabbit@rabbit1`과 함께 클러스터에 연결합니다. 그러기 위해 `rabbit@rabbit2`의 RabbitMQ 애플리케이션을 중지한 다음 `rabbit@rabbit1` 클러스터에 연결하고 다시 RabbitMQ 애플리케이션을 재시작합니다. 

노드가 기존 클러스터에 가입되려면 노드를 반드시 `reset` 해야 합니다. 노드를 `reset` 할 경우 **노드의 모든 자원과 데이터가 지워집니다.** 즉, 노드는 기존 데이터를 유지하면서 클러스터의 구성원이 될 수 없습니다. 

원하는 경우 백업 및 복원을 사용할 수 있습니다.

```powershell
# on rabbit2
$ rabbitmqctl stop_app

$ rabbitmqctl reset

$ rabbitmqctl join_cluster rabbit@rabbit1

$ rabbitmqctl start_app
```

위와 같이 명령을 하고 난 후 `cluster_status`를 통해 클러스터에 `rabbit@rabbit2` 노드가 연결된 것을 확인할 수 있습니다. 

```powershell
# on rabbit1
$ rabbitmqctl cluster_status
# => Cluster status of node rabbit@rabbit1 ...
# => [{nodes,[{disc,[rabbit@rabbit1,rabbit@rabbit2]}]},
# =>  {running_nodes,[rabbit@rabbit2,rabbit@rabbit1]}]
# => ...done.

# on rabbit2
$ rabbitmqctl cluster_status
# => Cluster status of node rabbit@rabbit2 ...
# => [{nodes,[{disc,[rabbit@rabbit1,rabbit@rabbit2]}]},
# =>  {running_nodes,[rabbit@rabbit1,rabbit@rabbit2]}]
# => ...done.
```

클러스터에 `rabbit@rabbit3` 노드를 연결하는 방법도 똑같지만 클러스터 연결 시 선택되는 노드가 중요하지 않다는 것을 보여주기 위해 `rabbit@rabbit2`에 연결하겠습니다.

```powershell
# on rabbit3
$ rabbitmqctl stop_app

$ rabbitmqctl reset

$ rabbitmqctl join_cluster rabbit@rabbit2

$ rabbitmqctl start_app
```

`cluster_status` 명령을 통해 모든 노드가 클러스터에 잘 연결되었음을 확인할 수 있습니다. 

```powershell
# on rabbit1
rabbitmqctl cluster_status
# => Cluster status of node rabbit@rabbit1 ...
# => [{nodes,[{disc,[rabbit@rabbit1,rabbit@rabbit2,rabbit@rabbit3]}]},
# =>  {running_nodes,[rabbit@rabbit3,rabbit@rabbit2,rabbit@rabbit1]}]
# => ...done.

# on rabbit2
rabbitmqctl cluster_status
# => Cluster status of node rabbit@rabbit2 ...
# => [{nodes,[{disc,[rabbit@rabbit1,rabbit@rabbit2,rabbit@rabbit3]}]},
# =>  {running_nodes,[rabbit@rabbit3,rabbit@rabbit1,rabbit@rabbit2]}]
# => ...done.

# on rabbit3
rabbitmqctl cluster_status
# => Cluster status of node rabbit@rabbit3 ...
# => [{nodes,[{disc,[rabbit@rabbit3,rabbit@rabbit2,rabbit@rabbit1]}]},
# =>  {running_nodes,[rabbit@rabbit2,rabbit@rabbit1,rabbit@rabbit3]}]
# => ...done.
```

### Restarting Cluster Nodes

클러스터에 포함된 노드는 언제든지 중지할 수 있고 OS에 의해 실패하거나 종료될 수 있습니다. 

일반적으로 노드가 중지된 후에도 대부분의 노드가 여전히 온라인 상태일 경우 클라이언트 연결 분배, queue의 복제 배치나 cluster의 load 분산은 변경되지만 클러스터의 나머지 부분에는 영향을 주지 않습니다.

### Schema Syncing from Online Peers

재시작된 노드는 부팅 시 peer의 스키마 및 기타 정보를 동기화합니다. 이 프로세스가 완료되기 전까지 노드는 **완전히 시작되거나  동작하지 않습니다.** 

그러므로 노드가 중지되고 재시작될 때 거치는 과정을 잘 이해하는 것이 중요합니다. 

중지된 노드는 다시 시작된 후에 동기화 할 클러스터 멤버를 선택합니다. 재시작 하면 노드는 기본적으로 30초의 응답 시간 초과로 해당 peer에 10번의 연결 시도를 합니다. 

해당 시간 간격으로 peer를 사용할 수 있게 되면, 노드는 정상적으로 실행되고 peer에서 필요한 것을 동기화 하고 계속해서 진행합니다. 

만약 peer를 사용할 수 없게 되면 재시작된 노드는 **포기하고 자발적으로 중지됩니다.** 이러한 상태는 로그에서 timeout (`timeout_waiting_for_tables`) 경고 메세지로 확인할 수 있고, 노드 시작이 실패합니다.

```powershell
2020-07-27 21:10:51.361 [warning] <0.269.0> Error while waiting for Mnesia tables: {timeout_waiting_for_tables,[rabbit@node2,rabbit@node1],[rabbit_durable_queue]}
2020-07-27 21:10:51.361 [info] <0.269.0> Waiting for Mnesia tables for 30000 ms, 1 retries left
2020-07-27 21:11:21.362 [warning] <0.269.0> Error while waiting for Mnesia tables: {timeout_waiting_for_tables,[rabbit@node2,rabbit@node1],[rabbit_durable_queue]}
2020-07-27 21:11:21.362 [info] <0.269.0> Waiting for Mnesia tables for 30000 ms, 0 retries left

2020-07-27 21:15:51.380 [info] <0.269.0> Waiting for Mnesia tables for 30000 ms, 1 retries left
2020-07-27 21:16:21.381 [warning] <0.269.0> Error while waiting for Mnesia tables: {timeout_waiting_for_tables,[rabbit@node2,rabbit@node1],[rabbit_user,rabbit_user_permission, …]}
2020-07-27 21:16:21.381 [info] <0.269.0> Waiting for Mnesia tables for 30000 ms, 0 retries left
2020-07-27 21:16:51.393 [info] <0.44.0> Application mnesia exited with reason: stopped

2020-07-27 21:16:51.397 [error] <0.269.0> BOOT FAILED
2020-07-27 21:16:51.397 [error] <0.269.0> ===========
2020-07-27 21:16:51.397 [error] <0.269.0> Timeout contacting cluster nodes: [rabbit@node1].
```

만약 노드가 종료되는 동안 온라인 peer가 없을 경우, 알려진 peer와 동기화를 시도하지 않고 시작됩니다. 그러나 독립 노드로 시작되지 않으며 peer가 다시 가입할 수 있습니다.

따라서 전체 클러스터가 중단되면 마지막으로 다운되는 노드는 종료 시 실행 중인 peer가 없는 유일한 노드입니다. 해당 노드는 peer에 먼저 연결하지 않고 시작될 수 있습니다. 모든 노드는 5분(default)간 알려진 peer에 연결을 시도하기 때문에 해당 시간 동안 임의의 순서로 노드를 재시작 할 수 있습니다. 이 경우 서로 성공적으로 다시 결합됩니다. 

응답 대기 시간이나 시도 횟수는 아래와 같이 설정할 수 있습니다.

```powershell
# 30초 대신 60초 동안 기다립니다
mnesia_table_loading_retry_timeout = 60000

# 10번 대신 15번 시도합니다
mnesia_table_loading_retry_limit = 15
```

### Restarts and Health Checks (Readiness Probes)

일부 환경에서 노드 재시작 시 지정된 `상태 확인`으로 제어됩니다. `상태 확인`은 한 노드가 실행되고 배포 프로세스가 다음 노드에서 진행할 수 있는지 확인합니다. 만약 검사가 통과되지 않으면 노드 배포가 완료되지 않은 것으로 간주되며 배포 프로세스는 일반적으로 일정 시간 동안 기다렸다가 다시 시도합니다. 

대부분의 `상태 확인`은 노드가 부팅을 완료했다고 암시적으로 가정합니다. peer로부터 스키마 테이블 동기화를 기다리는 노드는 적합하지 않습니다.

가장 일반적인 확인 커맨드

```powershell
$ rabbitmq-diagnostics check_running
```

노드가 완전히 부팅되고 스키마 테이블이 동기화 되지 않을 것 같은 상태 확인

```powershell
$ rabbitmq-diagnotics ping
```

### Hostname Changes Between Restarts

노드 이름이나 호스트명이 변경(만약 데이터 디렉토리 경로가 변경된 경우)된 후 재결합된 노드는 `빈 노드`로 시작될 수 있습니다. 이러한 노드는 클러스터에 재연결 되는데 실패합니다. 노드가 오프라인인 동안 해당 peer를 `reset` 하거나 빈 데이터 디렉토리로 시작할 수 있습니다. 이 경우 내부 데이터 저장소 클러스터 ID가 더 이상 일치하지 않으므로 복구 노드는 peer에 다시 가입하지 못합니다.

**시나리오**

1. 클러스터 구성 (A, B, C 노드)
2. 노드 A 종료
3. 노드 B `reset`
4. 노드 A 시작
5. 노드 A가 B에 재연결 시도하지만 B의 클러스터 ID 변경됨
6. 노드 B는 `reset` 되었기 때문에 A를 알려진 클러스터 구성원으로 인식하지 못함

이 경우 노드 B는 로그에 적절한 에러 메세지와 함께 A의 클러스터링 시도를 거부합니다.

```powershell
Node 'rabbit@node1.local' thinks it's clustered with node 'rabbit@node2.local', but 'rabbit@node2.local' disagrees
```

이 경우 B를 다시 재설정 한 다음 A에 가입할 수 있다거나 A를 재설정 하고  B에 성공적으로 가입할 수 있습니다.

### Cluster Node Restart Example

아래 예제는 `rabbit@rabbit1`과 `rabbit@rabbit3`을 종료하고 각 단계에서 클러스터 상태를 확인합니다.

```powershell
# on rabbit1
$ rabbitmqctl stop

# on rabbit2
$ rabbitmqctl cluster_status
# => Cluster status of node rabbit@rabbit2 ...
# => [{nodes,[{disc,[rabbit@rabbit1,rabbit@rabbit2,rabbit@rabbit3]}]},
# =>  {running_nodes,[rabbit@rabbit3,rabbit@rabbit2]}]
# => ...done.

# on rabbit3
rabbitmqctl cluster_status
# => Cluster status of node rabbit@rabbit2 ...
# => [{nodes,[{disc,[rabbit@rabbit1,rabbit@rabbit2,rabbit@rabbit3]}]},
# =>  {running_nodes,[rabbit@rabbit3,rabbit@rabbit2]}]
# => ...done.

# on rabbit3
rabbitmqctl stop
# => Stopping and halting node rabbit@rabbit3 ...done.

# on rabbit3
rabbitmqctl cluster_status
# => Cluster status of node rabbit@rabbit2 ...
# => [{nodes,[{disc,[rabbit@rabbit1,rabbit@rabbit2,rabbit@rabbit3]}]},
# =>  {running_nodes,[rabbit@rabbit2]}]
# => ...done.
```

아래 예제에서 노드가 다시 시작되면서 클러스터 상태를 확인합니다.

```powershell
# on rabbit1
rabbitmq-server -detached
rabbitmqctl cluster_status
# => Cluster status of node rabbit@rabbit1 ...
# => [{nodes,[{disc,[rabbit@rabbit1,rabbit@rabbit2,rabbit@rabbit3]}]},
# =>  {running_nodes,[rabbit@rabbit2,rabbit@rabbit1]}]
# => ...done.

# on rabbit2
rabbitmqctl cluster_status
# => Cluster status of node rabbit@rabbit2 ...
# => [{nodes,[{disc,[rabbit@rabbit1,rabbit@rabbit2,rabbit@rabbit3]}]},
# =>  {running_nodes,[rabbit@rabbit1,rabbit@rabbit2]}]
# => ...done.

# on rabbit3
rabbitmq-server -detached

# on rabbit1
rabbitmqctl cluster_status
# => Cluster status of node rabbit@rabbit1 ...
# => [{nodes,[{disc,[rabbit@rabbit1,rabbit@rabbit2,rabbit@rabbit3]}]},
# =>  {running_nodes,[rabbit@rabbit2,rabbit@rabbit1,rabbit@rabbit3]}]
# => ...done.

# on rabbit2
rabbitmqctl cluster_status
# => Cluster status of node rabbit@rabbit2 ...
# => [{nodes,[{disc,[rabbit@rabbit1,rabbit@rabbit2,rabbit@rabbit3]}]},
# =>  {running_nodes,[rabbit@rabbit1,rabbit@rabbit2,rabbit@rabbit3]}]
# => ...done.

# on rabbit3
rabbitmqctl cluster_status
# => Cluster status of node rabbit@rabbit3 ...
# => [{nodes,[{disc,[rabbit@rabbit1,rabbit@rabbit2,rabbit@rabbit3]}]},
# =>  {running_nodes,[rabbit@rabbit2,rabbit@rabbit1,rabbit@rabbit3]}]
# => ...done.

```

### Forcing Node Boot in Case of Unavailable Peers

경우에 따라 마지막으로 오프라인이 된 노드는 백업 할 수 없습니다. 해당 노드는 rabbitmqctl의 `forget_cluster_node` 명령을 통해 클러스터에서 지울 수 있습니다. 

또는 rabbitmqctl의 `force_boot` 명령을 사용하여 peer와 동기화 하지 않고 (마지막으로 종료된 것처럼) 노드를 부팅할 수 있습니다. 이것은 보통 종료될 마지막 노드나 노드 집합이 다시 온라인 상태로 돌아오지 못하게 할 경우에만 사용됩니다.

### Breaking Up a Cluster

때때로 클러스터에서 노드를 제거 해야 합니다. 운영자는 `rabbitmqctl` 명령을 사용하여 직접 이 작업을 수행해야 합니다. 

`rabbit@rabbit3`을 클러스터로부터 제거하여 독립적으로 운영될 수 있도록 해보겠습니다. 

그러기 위해서는 `rabbit@rabbit3`을 중지 시키고, 노드를 리셋하고, RabbitMQ 어플리케이션을 재시작 해야 합니다. 

```powershell
# on rabbit3
$ rabbitmqctl stop_app

$ rabbitmqctl reset

$ rabbitmqctl start_app
```

추가로 원격에서 노드를 제거할 수도 있습니다. 

클러스터에 `rabbit@rabbit1`, `rabbit@rabbit2`, `rabbit@rabbit3`이 있다고 할 때 원격에서 `rabbit@rabbit3`을 제거해보겠습니다.

```powershell
# on rabbit3
$ rabbitmqctl stop_app

# on rabbit2
$ rabbitmqctl forget_cluster_node rabbit@rabbit3

# on rabbit1
$ rabbitmqctl start_app
# => Starting node rabbit@rabbit3 ...
# => Error: inconsistent_cluster: Node rabbit@rabbit3 thinks it's clustered with node rabbit@rabbit1, but rabbit@rabbit1 disagrees

$ rabbitmqctl reset

$ rabbitmqctl start_app
```

### Hostname Changes

RabbitMQ 노드는 호스트명을 사용하여 서로 통신합니다. 그러므로 모든 노드의 이름으로 모든 클러스터 peer의 이름을 확인할 수 있어야 합니다. 

그 외에도 기본적으로 RabbitMQ 은 시스템의 현재 호스트명을 사용하여 데이터베이스 디렉토리 이름을 지정합니다. 만약 호스트명이 변경되면 비어 있는 새로운 데이터베이스가 생성됩니다. 데이터 손실을 방지하려면 고정되고 확인 가능한 호스트 이름을 설정하는 것이 중요합니다. 

호스트 명이 변경될 때마다 RabbitMQ 노드를 다시 시작해야 합니다. 

만약 노드 이름에 전체 호스트명을 작성하고 싶을 경우 `RABBITMQ_USE_LONGNAME=true` 환경 변수를 설정하면 됩니다. (기본으로 short name입니다)

### Connection to Clusters from Clients

클라이언트는 클러스터 내의 모든 노드에 연결될 수 있습니다. 만약 해당 노드가 실패하고 클러스터의 나머지 노드만 살아있는 경우 클라이언트는 연결이 닫힌 것을 알고, 클러스터의 다른 노드에 연결할 수 있어야 합니다. 

많은 클라이언트는 연결 시 순서대로 시도될 호스트명 리스트를 지원합니다. 

일반적으로 ip 주소를 클라이언트 응용 프로그램에 하드 코딩하는 것은 권장되지 않습니다. 이렇게 할 경우 유연성이 떨어지고 클러스터 구성이 변경되거나 클러스터의 노드 수가 변경되는 경우 클라이언트 응용 프로그램을 편집, 재컴파일, 재배포해야 합니다. 

이보다 추상적인 접근 방식을 고려하면 좋습니다. 

(로드밸런서 사용을 통해 가용성을 확보하는 것은 어떤가? haproxy…)

### Disk and RAM Nodes

노드는 *disk node*나 *RAM node*가 될 수 있습니다.  RAM 노드는 내부 데이터베이스 테이블을 RAM에만 저장합니다. 여기에는 메세지, 메세지 저장소 색인, Queue 색인 및 노드 상태 정보가 포함되지 않습니다. 

대부분의 경우 노드를 disk node로 사용하길 원합니다. RAM 노드는 Queue, exchange 또는 binding 전환이 많은 클러스터의 성능을 개선하는 데 사용할 수 있는 특수한 경우입니다. RAM 노드는 더 빠른 메세지 속도를 제공하지 않습니다. 만약 불확실할 경우 disk node를 사용하세요.

RAM node는 내부 데이터베이스를 RAM에만 저장하기 때문에 시작될 경우 peer node에서 동기화 해야 합니다. 즉, 클러스터는 최소 하나의 disk node는 포함해야 한다는 뜻입니다. 그러므로 클러스터의 마지막으로 남은 disk node를 수동으로 제거할 수 없습니다.

### Clusters with RAM nodes

RAM node는 메타데이터를 메모리에만 저장합니다. RAM node는 disk 노드만큼 disk에 쓸 필요가 없기 때문에 더 나은 성능을 낼 수 있습니다. 그러나, persistent queue 데이터는 항상 디스크에 저장되기 때문에 성능은 리소스 관리(queue, exchange, vhost 추가/제거)에만 영향을 미치고 publishing이나 consuming 속도에는 영향을 미치지 않습니다.

RAM 노드는 클러스터를 설정할 때 단순히 사용하지 않아야 합니다. 충분한 disk 노드가 필요하고 필요에 따라 확장을 위해 RAM 노드를 추가합니다. 

RAM 노드만 포함하는 클러스터는 매우 휘발성이 강하기 때문에 클러스터가 중지될 경우 다시 시작할 수 없으며 모든 데이터가 손실 됩니다. 

### Changing node types

node 유형을 ram↔ disc로 변경할 수 있습니다.

