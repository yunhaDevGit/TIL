## 1. elasticsearch + kibana 설치

docker-compose.yml 파일을 작성해줍니다.

**docker-compose.yml**

```yaml
version: '3'
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.11.1
    container_name: elasticsearch
    hostname: elasticsearch
    environment:
      - bootstrap.memory_lock=true
      - http.host=0.0.0.0
      - discovery.type=single-node
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - ../elk-config/conf/elasticsearch.yml:/usr/share/elasticsearch/elasticsearch.yml:ro
      - esdata:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
      - 9300:9300
    restart: always

  kibana:
    image: docker.elastic.co/kibana/kibana:7.11.1
    container_name: kibana
    hostname: kibana
    volumes:
      - ../elk-config/conf/kibana.yml:/usr/share/kibana/config/kibana.yml:ro
    ports:
      - 5601:5601
    restart: always
    depends_on:
      - elasticsearch
volumes:
  esdata:
    driver: local
```

docker-compose.yml 파일의 부모 디렉토리에 elk-config/config 디렉토리를 생성합니다.

config 디렉토리에 elasticsearch와 kibana의 실행 환경에 대한 설정을 정의해줍니다.

```
디렉토리 구조

docker
  ㄴ elk-config
    ㄴ config
        ㄴ elasticsearch.yml
        ㄴ kibana.yml
  ㄴ docker-compose.yml
```

**elasticsearch.yml**

```yaml
sticsearch.password: espassword
cluster.name: docker-elk-cluster
node.name: node-1
path.data: /usr/share/elasticsearch/data
http.port: 9200
```

**kibana.yml**

```yaml
server.host: 0.0.0.0
server.port: 5601
elasticsearch:
  hosts: ["http://192.168.120.6:9200"]
  username: elastic
  password: espassword

status.allowAnonymous: true
```

설정 파일들이 잘 작성 되었다면, docker-compose up을 수행해줍니다.

```bash
docker-compose up -d
```

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/2027bd2b-dd06-4886-9c38-e44b81d2d311/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211206%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211206T064514Z&X-Amz-Expires=86400&X-Amz-Signature=fc515309e855b95b94caf05fed9253c87a97df8318e74453649295b2e51e4c7b&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

`docker ps` 명령으로 실행된 컨테이너를 확인할 수 있습니다.

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/7b2b4020-8fa7-4dc6-a436-5f25e4ac11e6/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211206%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211206T064531Z&X-Amz-Expires=86400&X-Amz-Signature=538b50ec96e0a2282a0d034c3aab7386b8f4000e4591d0833adfc048ccf567fa&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

kibana가 정상적으로 잘 동작하는지 확인하기 위해 kibana.yml에 작성해준 hosts에 접속해봅니다.

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/3768a922-066b-4737-8966-cc0852e33d23/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211206%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211206T064545Z&X-Amz-Expires=86400&X-Amz-Signature=8631fa5ccdebe0727c544b646b6f759ed3f607529eace518b46ce777bc011790&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

## 2. filebeat 설치

elasticsearch, kibana와 마찬가지로 filebeat도 docker를 사용하여 설치한다.

**docker-compose.yml**

```yaml
version: '3'

services:
  filebeat:
    image: docker.elastic.co/beats/filebeat:7.11.1
    container_name: filebeat
    hostname: filebeat-log1
    user: root
    volumes:
      - ../elk-config/conf/filebeat.yml:/usr/share/filebeat/filebeat.yml:ro
      - fbdata:/usr/share/filebeat/data/
      - /var/log/:/logs/host/
    restart: always

volumes:
  fbdata:
    driver: local
```

filebeat에 대한 실행 환경 설정은 elasticsearch.yml, kibana.yml과 동일한 디렉토리에 생성해준다

**filebeat.yml**

```yaml
# input
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /logs/host/messages*
# multiline.pattern: ^\[

# for kibana dashboard
setup.kibana:
  host: "192.168.120.6:5601"
  protocol: "http"
setup.dashboards.enabled: true

# output
output.elasticsearch:
  hosts: ["192.168.120.6:9200"]
  username: "elastic"
  password: "espassword"
```

docker-compose.yml up -d 명령어를 통해 docker-compose를 실행 후 filebeat가 실행됐는지 확인한다.

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/0d4c51ab-58e3-43ff-b8a8-730910051654/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211206%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211206T064601Z&X-Amz-Expires=86400&X-Amz-Signature=37d1b2239f5ed2cdd2465baf37558482ef12ecf5a34aec50cb6c4a6d8593212e&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

## 3. kibana 접속하여 모니터링

- kibana 접속 URL : http://192.168.120.6:5601/

위 URL에 접속하여 Logs 메뉴를 선택하여 실시간으로 수집되고 있는 로그를 볼 수 있다.

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/c55e11f3-b5d5-499a-aecb-c3b639a7d97a/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211206%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211206T064612Z&X-Amz-Expires=86400&X-Amz-Signature=a8bb5503450d43eea9c4f2e48d6b83ce5848cc8c7a69d6d2c6f0ec8aee105af3&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/8ec17273-3260-4316-ad39-f513fd3328aa/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211206%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211206T064621Z&X-Amz-Expires=86400&X-Amz-Signature=6c7a1220aa902bb4848c18a81c6673a5f8549d9d0603e167369b7423fa40bcee&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

### 정상적으로 동작하는지 확인...

**$ tail -f /var/log/messages**

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/c3676a7e-e697-4ccd-861e-7628d2ee57c8/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211206%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211206T064633Z&X-Amz-Expires=86400&X-Amz-Signature=6c4cad9c10454a60df525177258aa5a0310b5a49c6d5909ab6a72778830a9d21&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

log가 증적되는 파일을 tail -f 명령어를 통해 실시간으로 확인한다.

kibana 페이지를 켠 후 우측 상단의 `Stream live` 버튼을 눌러 5초마다 주기적으로 로그 수집을 수행한다.

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/cd0c8240-bd3c-4094-8695-6c20ce184d07/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211206%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211206T064651Z&X-Amz-Expires=86400&X-Amz-Signature=a8152a23e8fc7fe7a482743cf9f508d32754a2e9c0cf2a2ecc99fc24ae77ff3f&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

그렇게 출력 되는 log와 filebeat.yml에 설정한 경로의 파일 로그가 동일하게 잘 수집 되는지 확인한다.

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/6e77bfe6-e6b9-444a-b02a-e29673b1a0f8/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211206%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211206T064709Z&X-Amz-Expires=86400&X-Amz-Signature=e20cb32f277a213f55b86d1f437ca1994b1ffb7e778ca257f47042b048eeb278&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

만약 수집하고자 하는 로그 파일 경로를 변경하고 싶을 경우 filebeat.yml의 paths를 변경해준 후 filebeat 컨테이너를 재시작하면 된다.
