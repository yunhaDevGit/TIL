# docker-compose
## docker-compose 사용이유

여러 개의 컨테이너가 하나의 애플리케이션에서 동작할 때 이를 테스트하려면 각 컨테이너를 하나씩 생성해야 한다.
여러 개의 컨테이너로 구성된 애플리케이션을 구축하기 위해 run 명령어를 여러 번 사용할 수 있지만, 각 컨테이너가 제대로 동작하는지 확인하는 테스트 단계에서는 이렇게 하기 번거롭습니다. 
매번 run 명령어에 옵션을 설정해 CLI로 컨테이너를 생성하기보다는 여러개의 하나의 서비스로 정의해 컨테이너 묶음으로 관리할 수 있다면 좀 더 편리할겁니다.
**docker-compose는 여러 개의 컨테이너를 하나의 프로젝트로서 다룰 수 있는 작업 환경을 제공합니다.**
docker-compose는 run 명령어의 옵션을 그대로 사용할 뿐만 아니라 각 컨테이너의 의존성, 네트워크, 볼륨 등을 함께 정의할 수 있습니다.


## docker-compose 설치
```
# docker-compose 설치
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# docker-compose 실행 권한 부여
$ sudo chmod +x /usr/local/bin/docker-compose

# 설치된 docker-compose 실행 확인
$ docker-compose --version

```
https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-centos-7


## docker-compose 사용법
### docker-compose.yml 예시
```yml
version: "3"

services:
  mariadb:
    image: mariadb:10.5.10-focal
    restart: always
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: cloudit
      TZ: Asia/Seoul
    volumes:
      - ./mariadb/data:/var/lib/mysql
      - ./mariadb/config:/etc/mysql/conf.d
  influxdb:
    image: influxdb:1.8.6
    restart: always
    ports:
      - "8086:8086"
    environment:
      INFLUXDB_DB: cloudit
    volumes:
      - ./influxdb:/var/lib/influxdb
  zookeeper:
    image: zookeeper:3.7.0
    restart: always
    ports:
      - "2181:2181"
    environment:
      ZOO_MY_ID: 1
      ZOO_SERVERS: server.1=0.0.0.0:2888:3888;2181
```

### docker-compose.yml 실행
```
docker-compose -f docker-compose-test.yml up
```

-f : 기본으로 docker-compose.yml이라는 이름을 설정 파일로 사용하지만, 다른 이름이나 경로의 파일을 Docker Compose 설정 파일로 사용하고 싶을 경우 명시
        
