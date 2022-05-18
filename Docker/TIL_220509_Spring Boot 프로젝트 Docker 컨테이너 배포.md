# Spring Boot 프로젝트 Docker 컨테이너 배포

테스트 서버를 구축하기 위해 Spring Boot 프로젝트를 Docker 컨테이너로 배포하는 작업에 대한 과정

### 1. 애플리케이션 빌드

![image](https://user-images.githubusercontent.com/74949294/167332037-c350270c-db11-4f15-b90d-b647634f72b2.png)

- gradle은 test를 제외하고 빌드 하기 위해 위와 같은 설정을 해주면 된다.

![image](https://user-images.githubusercontent.com/74949294/167332067-db39f9d8-ba2d-4d0f-8e00-0ea68d55e3fa.png)

- 빌드 실행

![image](https://user-images.githubusercontent.com/74949294/167332094-3b71176a-0331-4bea-b7a4-d5aff47fec1d.png)

- 빌드가 완료되면 `build > libs`에 jar가 생긴다.
    - 만약 해당 경로에 `앱이름-plain.jar`가 생성될 경우 build.gradle에 아래 설정을 추가해주면 된다.
        
        ```json
        jar {
            enabled = false
        }
        ```
        
        - 앱이름.jar → bootJar Task로 생성된 것으로 해당 프로젝트에 필요한 모든 의존성이 같이 추가된 것으로 MANIFEST.MF까지 모두 정상적인 형태
        - 앱이름-plain.jar →build Task로 생성된 것으로 의존성을 제외하고 딱 프로젝트에 있는 자원들만 jar로 만든 것으로 spring 관련 의존성이 빠져 MANIFEST.MF에 Main 메서드의 위치가 나오지 않는다
        

### 2. Dockerfile 생성

![image](https://user-images.githubusercontent.com/74949294/167332230-bbc88d23-e837-4110-8b97-b51c8505533e.png)

![image](https://user-images.githubusercontent.com/74949294/167332243-fe688eb6-8550-483e-95f6-dd4f9b02bcf7.png)

```docker
FROM openjdk:11.0.13-jdk-slim
ARG JAR_FILE=build/libs/*.jar
COPY ${JAR_FILE} app.jar
EXPOSE 9696
ENTRYPOINT ["java","-jar","/app.jar"]
```

- FROM : 베이스 이미지
    - 어느 이미지에서 시작할 것인지를 의미합니다.
- ARG : bulid 시점에 활용되는 변수 선언문
    - JAR_FILE이라는 변수에 build/libs/*.jar라는 표현식을 담았습니다.
- COPY : build 명령 중간에 호스트의 파일 또는 폴더를 이미지에 가지고 옵니다.
- EXPOSE : Dockerfile의 빌드로 생성된 이미지에서 열어줄 포트를 의미
    - 호스트 머신과 컨테이너의 포트 매핑 시 사용됩니다.
    - 컨테이너 생성 시 `-p` 옵션의 컨테이너 포트 값으로 EXPOSE 값을 적어야 합니다.
- ENTRYPOINT : 컨테이너를 생성, 실행 할 때 실행할 명령어
    - jar 파일을 실행합니다.

### 3. Dockerfile 빌드

> **Dockerfile이란?**
Docker Image를 생성하기 위한 스크립트(설정 파일)
> 

```bash
docker build -t cloudit-network:0.0.1-SNAPSHOT .
```

![image](https://user-images.githubusercontent.com/74949294/167332254-866d6512-e283-45ee-9060-a059047a992e.png)

![image](https://user-images.githubusercontent.com/74949294/167332263-37e5524d-c1ab-4dcf-8169-e341c370ccb2.png)

### 4. 생성 된 docker image를 다른 서버에 전송

(만약 동일 서버 내에서 컨테이너를 실행할 경우 해당 과정은 생략 가능합니다)

**4.1 docker image → tar 파일 변환**

생성된 docker image를 다른 서버로 전송하기 위해 docker image를 파일 형태로 저장합니다. 

**docker image → tar 파일**

```bash
$ docker save [옵션] <파일명> [이미지명]
$ docker save -o cloudit-network.tar cloudit-6.0-network:0.0.1-SNAPSHOT
```

-o : 저장할 파일명 지정

![image](https://user-images.githubusercontent.com/74949294/167332284-5daa7176-90da-4e60-9774-e82526820b73.png)

지정한 파일명으로 tar 파일이 생성된 것을 확인할 수 있습니다. 

**4.2 생성된 tar 파일 다른 서버로 전송**

```bash
$ scp <전송할 파일> [계정]@[전송할 서버 ip]:/{경로}
```

![image](https://user-images.githubusercontent.com/74949294/167332297-7fd55bac-e225-414e-9174-96d8a5cfa197.png)

**4.3 tar 파일 → docker image 변환**

tar 파일로 만들어진 이미지를 다시 docker image로 되돌리기 위해서 docker load 커맨드를 사용하면 됩니다.

```bash
$ docker load -i <tar 파일명>
```

![image](https://user-images.githubusercontent.com/74949294/167332317-3f4b4e7e-cbe9-44dd-9255-42de5bd38383.png)

![image](https://user-images.githubusercontent.com/74949294/167332324-82515d7b-b2da-427b-830a-e930e9f600d5.png)

### 5. docker image 실행
```bash
$ docker run -itd --name={컨테이너 이름} --network={네트워크 타입} -p[host port]:[container port] [image 이름][:태그][인수]
$ docker run -itd --name=cloudit-network --network=host -p9696:9696 cloudit-6.0-network:0.0.1-SNAPSHOT
```

![image](https://user-images.githubusercontent.com/74949294/167332349-7a956f0f-fe7f-49a7-ae47-1ad72c5eb73b.png)
