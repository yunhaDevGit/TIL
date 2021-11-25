# Kafka 명령어


```
kafka 위치 - docker/kafka_2.12-2.5.0/
```

### Topic 생성 명령어

```bash
{kafka 위치}/bin/kafka-topic.sh --create --bootstrap-server localhost:9092 --topic {topic 이름}
```

### Topic list 확인 명령어

```bash
{kafka 위치}/bin/kafka-topics.sh --list --bootstrap-server localhost:9092
```

### Topic detail 조회 명령어

```bash
{kafka 위치}/bin/kafka-topics.sh --bootstrap-server localhost:9092 --topic {topic 이름} --describe
```

### Topic 삭제 명령어

```bash
{kafka 위치}/bin/kafka-topics.sh --delete --zookeeper localhost:2181 --topic {topic-name}
```

Topic 삭제가 되지 않는다면 아래와 같이 server.properties에 해당 설정을 추가해줘야 한다. 

```bash
vi {kafka 위치}/config/server.properties

delete.topic.enable = true
```
