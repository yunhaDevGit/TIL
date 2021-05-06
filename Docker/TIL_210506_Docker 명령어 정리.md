# Docker 명령어 정리



#### 컨테이너 확인

- docker ps
- docker ps -a (중지 중인 컨테이너도 확인)



#### 컨테이너 시작

- docker start [컨테이너 id]



#### 컨테이너 중지

- docker stop [컨테이너 id]



#### 컨테이너 삭제

- docker rm [컨테이너 id]
- docker rm `docker ps -a -q` (모든 컨테이너 삭제)



#### 컨테이너 접속

- docker attach [컨테이너 이름 혹은 아이디]



#### Image 확인

- docker  images



#### Image 삭제

- docker rmi [이미지 id]
- docker rmi $(docker images | awk '{print $1}')



#### Image 다운로드

- docker pull [이미지 이름]



#### Image 실행

- docker run [옵션] [이미지 이름 또는 아이디] [실행 파일]
  - docker run --name mariadb -d -p 4000:3306 -3 MYSQL_ROOT_PASSWORD=password mariadb
    - -d : 컨테이너를 일반 프로세스가 아닌 데몬 프로세스 형태로 실행
    - -t : 가상 터미널 환경을 애뮬레이션 한다
    - -i :  사용자가 입출력을 할 수 있는 상태로 한다



- 백그라운드로 생성한 컨테이너에 접속
  - docker exec -i -t [컨테이너 이름] /bin/bash



