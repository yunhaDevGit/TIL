# MQTT Plugin

RabbitMQ는 MQTT plugin을 지원합니다. 

MQTT 클라이언트는 다른 프로토콜과 함께 운영할 수 있습니다. 

## 플러그인 활성화

MQTT 플러그인은 RabbitMQ 배포판에 포함되어 있습니다. 

클라이언트가 성공적으로 연결하려면 `Rabbitmq-plugins`를 사용하여 활성화 해야 합니다.

```bash
rabbitmq-plugins enable rabbitmq_mqtt
```

위와 같이 plugin을 enabled 상태로 바뀝니다. MQTT 클라이언트는 적절한 권한이 있는 기존 사용자에게 자격 증명이 있는 경우 연결할 수 있습니다.

### **[사용자 및 인증](https://www.rabbitmq.com/mqtt.html#authentication)**

MQTT 연결이 성공하려면 인증에 성공해야 하며 사용자는 플러그인에서 사용하는 가상 호스트에 대한 적절한 권한이 있어야 합니다. 

MQTT 클라이언트는 연결할 때 자격 증명을 지정할 수 있습니다. 

플러그인은 익명 인증을 지원하지만 사용을 권장하지 않으며, 기본적으로 합리적인 수준의 보안을 위해 실행되는 특정 제한 사항은 아래와 같습니다. 

사용자 및 권하는 `rabbitmqctl`, `관리 UI` 또는 `HTTP API`를 사용하여 관리할 수 있습니다. 

예를 들어 아래의 명령은 이 플러그인에서 사용하는 기본 **가상 호스트에 대한 전체 액세스 권한이 있는 MQTT  연결에 대한 새 사용자를 생성합니다.**

```
# 사용자 이름과 비밀번호는 모두 "mqtt-test" 입니다.
rabbitmqctl add_user mqtt-test mqtt-test
rabbitmqctl set_permissions -p / mqtt-test".*"".*"".*"
rabbitmqctl set_user_tags mqtt-test management
```

## 동작 방식

RabbitMQ MQTT 플러그인은 MQTT를 대상으로 하며 광범위한 MQTT 클라이언트를 지원합니다. 

플러그인은 RabbitMQ 핵심 프로토콜의 엔티티인 교환 및 큐를 기반으로 합니다. MQTT topic에 게시 된 메세지는 내부적으로 topic exchange(default, `amqp.topic`)을 사용합니다. 구독자
