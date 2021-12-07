## **ELK 로그 수집을 위한 filebeat 설치**

ELK에서 로그를 수집하기 위해 filebeat을 에이전트 노드에 설치

[다운로드](https://www.elastic.co/kr/downloads/beats/filebeat)

```bash
wget {다운로드 경로}
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f76adf55-5b48-4551-8e5e-120c6e5ef3e6/Untitled.png)

```bash
rpm -ivh {다운로드 된 rpm file}
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4cb1d01f-e774-4113-85a3-a5076ef57660/Untitled.png)

## filebeat 설정

`/etc/filebeat/filbeat.yml` 에서 filebeat에 대한 설정을 할 수 있다.

(아래는 중요한 설정에 대해서만 보여준다)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dd9daa04-fc5f-49a6-b8ee-a8dfdb5f993f/Untitled.png)

→ 위의 설정 정보가 담기면 된다....

```yaml
# ============================== Filebeat inputs ===============================

filebeat.inputs:

# Each - is an input. Most options can be set at the input level, so
# you can use different inputs for various configurations.
# Below are the input specific configurations.

- type: log

  # Change to true to enable this input configuration.
  enabled: true

  # Paths that should be crawled and fetched. Glob based paths.
  paths:
#    - /var/log/*.log
    - /var/log/cloudit/*.log
    #- c:\programdata\elasticsearch\logs\*

# ============================== Filebeat modules ==============================

filebeat.config.modules:
  # Glob pattern for configuration loading
  path: ${path.config}/modules.d/*.yml

  # Set to true to enable config reloading
  reload.enabled: false

  # Period on which files under path should be checked for changes
  #reload.period: 10s

# ======================= Elasticsearch template setting =======================

setup.template.settings:
  index.number_of_shards: 1
  #index.codec: best_compression
  #_source.enabled: false

# ================================= Dashboards =================================
# These settings control loading the sample dashboards to the Kibana index. Loading
# the dashboards is disabled by default and can be enabled either by setting the
# options here or by using the `setup` command.
setup.dashboards.enabled: true

# =================================== Kibana ===================================

# Starting with Beats version 6.0.0, the dashboards are loaded via the Kibana API.
# This requires a Kibana endpoint configuration.
setup.kibana:

  # Kibana Host
  # Scheme and port can be left out and will be set to the default (http and 5601)
  # In case you specify and additional path, the scheme is required: http://localhost:5601/path
  # IPv6 addresses should always be defined as: https://[2001:db8::1]:5601
  host: "192.168.120.6:5601"
  protocol: "http"

  # Kibana Space ID
  # ID of the Kibana Space into which the dashboards should be loaded. By default,
  # the Default Space will be used.
  #space.id:

# ---------------------------- Elasticsearch Output ----------------------------
output.elasticsearch:
  # Array of hosts to connect to.
  hosts: ["192.168.120.6:9200"]

  # Protocol - either `http` (default) or `https`.
  #protocol: "https"

  # Authentication credentials - either API key or username/password.
  #api_key: "id:api_key"
  username: "elastic"
  password: "espassword"
```

### filebeat 로그 수집 설정

```bash
filebeat modules list
```

위의 command를 사용하여 설치 이후 활성화된 모듈과 비활성화 된 모듈을 확인할 수 있다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/643f3d20-ac23-4d23-924c-21b99e6fed5a/Untitled.png)

### 모듈 활성화

```bash
filebeat modules enable {모듈 이름}
```

위의 명령을 통해 원하는 모듈을 활성화 시킬 수 있습니다.
