# CentOS에 tomcat 설치

### 1. Tomcat 다운로드

[https://tomcat.apache.org/](https://tomcat.apache.org/)

톰캣 공식 사이트에서 wget으로 zip 파일을 다운로드 받습니다.

```bash
$ wget https://dlcdn.apache.org/tomcat/tomcat-8/v8.5.83/bin/apache-tomcat-8.5.83.zip -P /usr/local/tomcat8
```

![image](https://user-images.githubusercontent.com/74949294/195533833-05a48c22-9bc6-4941-a7e8-2720f34f663a.png)

### 2. 압축 풀기

```bash
$ unzip apache-tomcat-8.5.83.zip

// 안에 있는 내용 tomcat8 디렉토리로 이동
$ cd apache-tomcat-8.5.83
$ mv * /usr/local/tomcat
```

정상적으로 압축이 풀렸을 경우 `bin` 폴더의 모든 shell script에 실행 권한을 부여합니다.

```bash
$ cd /usr/local/tomcat/bin
$ chmod +x *.sh
```

### 3. tomcat service 등록

`/etc/systemd/system` 경로에 `tomcat.service` 파일을 만든 후 아래와 같이 작성해줍니다.

```bash
$ vi tomcat.service

[Unit]
Description=Tomcat 8.5 servlet container
After=network.target

[Service]
Type=forking

User={계정}
Group={그룹}

Environment="JAVA_HOME=/usr/lib/jvm/jre"
Environment="JAVA_OPTS=-Djava.security.egd=file:///dev/urandom"

Environment="CATALINA_BASE=/usr/local/tomcat8/"
Environment="CATALINA_HOME=/usr/local/tomcat8/"
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

ExecStart=/usr/local/tomcat8//bin/startup.sh
ExecStop=/usr/local/tomcat8/bin/shutdown.sh

[Install]
WantedBy=multi-user.target
```

서비스 등록 및 재시작을 합니다.

```bash
$ systemctl daemon reload
$ systemctl tomcat start
```

![image](https://user-images.githubusercontent.com/74949294/195533876-72985cb0-14d6-4a5f-9f0b-32ac4a3dc559.png)

아래와 같이 8080 포트가 정상적으로 떴을 경우 정상적으로 톰캣 설치 및 실행이 완료 된 겁니다. 

[http://localhost:8080](http://localhost:8080/) 접속 시 아래와 같이 톰켓 페이지가 나옵니다. 

![image](https://user-images.githubusercontent.com/74949294/195533910-cac3cdd1-b01a-4529-b65e-51b1a62a892a.png)

![image](https://user-images.githubusercontent.com/74949294/195533949-af46835b-8f37-4acd-a945-a8c5d67f8ec1.png)


### 4. tomcat manager 계정 및 패스워드 설정

아래의 `Manager App` 버튼을 누를 경우, 톰캣 매니저 화면으로 이동하게 되는데 해당 페이지에서 웹 서비스 항목들의 서비스 여부를 관리하면 됩니다. 

![image](https://user-images.githubusercontent.com/74949294/195533993-14cb77f3-6f55-418d-a852-cd85ca01af0d.png)

처음 접속 시 아래와 같이 `username`과 `password`를 입력하라고 나오는데 계정의 경우 아래와 같이 설정할 수 있습니다.

![image](https://user-images.githubusercontent.com/74949294/195534021-4625fdf7-39e1-4f05-9f0c-6f8fb485e667.png)

`/usr/local/tomcat8/conf/tomcat-users.xml` 파일을 보면 아래와 같이 `<user username~ />` 설정 부분이 주석 처리 되어 있습니다. 

해당 부분에 원하는 **username**과 **password**를 입력하면 됩니다. 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
  <user username="admin" password="admin" roles="manager-gui"/> <!-- 해당 부분 추가 -->
</tomcat-users>
```

위와 같이 tomcat manager 계정 및 패스워드 설정이 완료 되었을 경우, tomcat 서비스를 재시작 해줍니다. 

```bash
$ systemctl restart tomcat
```

### 5. tomcat 매니저 접속

톰캣 매니저 접속 시 아래와 같은 화면을 볼 수 있습니다.

![image](https://user-images.githubusercontent.com/74949294/195534061-77b03641-8c5c-4cb5-b85c-66b9cc34ff50.png)
