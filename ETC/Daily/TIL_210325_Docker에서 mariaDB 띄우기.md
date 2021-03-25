# Docker에서 mariaDB 띄우기

**docker 시작**

- systemctl start docker



**docker 명령어**

- 컨테이너 확인
  - docker ps
  - docker ps -a
- 이미지 확인
  - docker images
- 컨테이너 시작/중지
  - docker start [컨테이너 id]
  - docker stop [컨테이너 id]
- 컨테이너 삭제
  - docker rm [컨테이너 id]
  - docker rm `docker ps -a -` : 모든 컨테이너 삭제
- 이미지 삭제
  - docker rmi [이미지 id]
- 이미지를 컨테이너로 만들고 시작
  - docker run [option] [이미지]:[태그] [명령어]



### MariaDB 시작

mariaDB 이미지 다운로드

- docker pull mariadb 

이미지 확인

- docker image ls

mariaDB 이미지 실행

- docker run --name mariadb -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=mariadb mariadb

  (docker run --name [컨테이너 이름] -d -p 입력포트:접속포트 -e MYSQL_ROOT_PASSWORD=[비밀번호] [이미지 이름])

컨테이너에 접속

- docker exec -it mariadb /bin/bash

mariadb 접속

- mysql -uroot -pmariadb

