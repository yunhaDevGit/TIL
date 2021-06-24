### mariadb image 다운로드
docker pull mariadb

### image 실행
docker run --name mariadb -d -p 3307:3307 -e MYSQL_ROOT_PASSWORD=cloudit mariadb
docker run --name [컨테이너 이름] -d -p 입력 포트:접속 포트 -e MYSQL_ROOT_PASSWORD=[비밀번호] [이미지 이름]

### cloudit-database.sql을 docker mariaDB로 이동(sql 파일 위치에서)
docker cp cloudit-database.sql 291d04cb10c2:/cloudit-database.sql
docker cp [sql 파일 이름].sql [컨테이너 ID]:/[sql 파일 이름].sql

### 실행
docker exec -it mariadb /bin/bash

### sql 파일 실행
source /cloudit-database.sql;
source [파일 경로]/[sql 파일 이름].sql;
