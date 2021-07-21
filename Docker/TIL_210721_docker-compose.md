# docker compose 사용법

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
        
