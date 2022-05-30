# RabbitMQ 설치하기

## RabbitMQ 설치

### 1. EPEL 저장소 설치

```
$ yum install -y epel-release
```

### 2. erlang 설치 및 확인

**erlang 설치**

클러스터 구성된 노드끼리 통신을 위해 Erlang Cookie가 필요하므로 erlang을 설치합니다.

```
$ yum install -y erlang
```

**erlang 설치 확인**

`erl` 명령어 입력 시 아래와 같이 나오면 잘 설치된겁니다.

```
$ erl

Erlang/OTP 23 [erts-11.2] [source] [64-bit] [smp:1:1] [ds:1:1:10] [async-threads:1] [hipe]

Eshell V11.2  (abort with ^G)
1>
```

### 3. RabbitMQ 설치 및 실행

```
$ yum install -y rabbitmq-server
$ systemctl start rabbitmq-server
```

### 4. RabbitMQ Management 활성화

```
$ rabbitmq-plugins enable rabbitmq_management
```

### 5. RabbitMQ 계정 추가

기본 계정 - id : guest / password : guest

```
// 사용자 계정 추가 (rabbitmqctl add_user <사용자> <비번>
$ rabbitmqctl add_user admin admin

// 사용자에게 태그 설정 (rabbitmqctl set_user_tags <사용자> <태그>
$ rabbitmqctl set_user_tags admin administrator

// 사용자 접속 퍼미션 설정 (rabbitmqctl set_permissions -p / <사용자> <접속퍼미션>
$ rabbitmqctl set_permissions -p / admin ".*" ".*" ".*"

// 사용자 목록 확인
$ rabbitmqctl list_users

// RabbitMQ 재시작
$ systemctl restart rabbitmq-server
```

### 6. RabbitMQ Web Console에 접속

정상적으로 RabbitMQ가 설치된 경우 아래와 같이 RabbitMQ Management를 통해 노드 및 큐 정보를 확인할 수 있습니다.

`http://{server-ip}:15672`

해당 Web Console 접속은 위에서 설정한 계정/비밀번호 사용하면 됩니다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c6fd42e3-68da-4310-a071-5b9493d5f392/Untitled.png)

## RabbitMQ Cluster 구성하기

위의 방법을 통해 RabbitMQ를 클러스터링 할 모든 노드에 설치합니다.

2대의 노드를 사용하여 RabbitMQ Broker를 클러스터링 하는 방법입니다.

### /etc/hosts에 hostname 설정

모든 노드의 `/etc/hosts` 파일에 대상 노드의 호스트네임 설정을 합니다.

```
# all node
$ vi /etc/hosts
192.168.120.6 server1
192.168.120.7 server2
```

### node 1에 node 2 연결

더 많은 노드를 클러스터에 포함시키고 싶을 경우 해당 노드에서 아래의 작업을 하면 됩니다.

```
# rabbitmq node 2
$ rabbitmqctl stop_app

$ rabbitmqctl reset

$ rabbitmqctl join_cluster rabbit@server1

$ rabbitmqctl start_app

$ rabbitmqctl cluster_status
```

### RabbitMQ Web Console 확인
