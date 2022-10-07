# MariaDB 실행 오류

docker를 통해 MariaDB 실행 시 아래와 같은 에러와 함께 계속해서 restarting 상태에 빠졌었다.

```bash
2022-10-07T01:47:53.978802787Z 221007 10:47:53 InnoDB: The InnoDB memory heap is disabled
2022-10-07T01:47:53.978814285Z 221007 10:47:53 InnoDB: Mutexes and rw_locks use GCC atomic builtins
2022-10-07T01:47:53.978816381Z 221007 10:47:53 InnoDB: Compressed tables use zlib 1.2.7
2022-10-07T01:47:53.978818115Z 221007 10:47:53 InnoDB: Using Linux native AIO
2022-10-07T01:47:53.983134217Z 221007 10:47:53 InnoDB: Initializing buffer pool, size = 256.0M
2022-10-07T01:47:53.992452797Z 221007 10:47:53 InnoDB: Completed initialization of buffer pool
2022-10-07T01:47:53.996506080Z InnoDB: Error: checksum mismatch in data file ./ibdata1
2022-10-07T01:47:53.996515447Z 221007 10:47:53 InnoDB: Could not open or create data files.
2022-10-07T01:47:53.996517734Z 221007 10:47:53 InnoDB: If you tried to add new data files, and it failed here,
2022-10-07T01:47:53.996519431Z 221007 10:47:53 InnoDB: you should now edit innodb_data_file_path in my.cnf back
2022-10-07T01:47:53.996521136Z 221007 10:47:53 InnoDB: to what it was, and remove the new ibdata files InnoDB created
2022-10-07T01:47:53.996522790Z 221007 10:47:53 InnoDB: in this failed attempt. InnoDB only wrote those files full of
2022-10-07T01:47:53.996524353Z 221007 10:47:53 InnoDB: zeros, but did not yet use them in any way. But be careful: do not
2022-10-07T01:47:53.996526153Z 221007 10:47:53 InnoDB: remove old data files which contain your precious data!
2022-10-07T01:47:53.996527705Z 221007 10:47:53 [ERROR] Plugin 'InnoDB' init function returned error.
2022-10-07T01:47:53.996529268Z 221007 10:47:53 [ERROR] Plugin 'InnoDB' registration as a STORAGE ENGINE failed.
2022-10-07T01:47:54.002592146Z 221007 10:47:54 [Note] Plugin 'FEEDBACK' is disabled.
2022-10-07T01:47:54.002629032Z 221007 10:47:54 [ERROR] Can't open the mysql.plugin table. Please run mysql_upgrade to create it.
2022-10-07T01:47:54.002636121Z 221007 10:47:54 [ERROR] Unknown/unsupported storage engine: InnoDB
2022-10-07T01:47:54.002638149Z 221007 10:47:54 [ERROR] Aborting
2022-10-07T01:47:54.002639829Z 
2022-10-07T01:47:54.012677112Z 221007 10:47:54 [Note] mysqld: Shutdown complete
2022-10-07T01:47:54.012686491Z
```

**/etc/my.cnf 설정 변경**

아래와 같이 mysql 데이터 저장 경로를 변경하였더니 잘 동작한 줄 알았는데….잠깐 올라왔다가 또 내려갔다.

```bash
datadir=/data/mysql
```

docker-compose.yml 설정이 이상한가 싶어서 다시 확인한 후 `MYSQL_ROOT_PASSWORD` 설정과 `./mariadb/data를 /data/mysql`로 매핑하는 볼륨 설정을 추가해준 후에 다시 실행해보았다. 

**기존 설정**

```yaml
version: '3'
services:
  mariadb:
    image: mariadb:5.5.48
		retries: always
    ports:
      - "3308:3306"
    environment:
      MYSQL_PASSWORD: cloudit
      TZ: Asia/Seoul
    volumes:
      - ./mariadb/data:/var/lib/mysql
      - /etc/mysql/my.cnf:/etc/my.cnf
```

**변경 후 설정**

```yaml
version: '3'
services:
  mariadb:
    image: mariadb:5.5.48
    ports:
      - "3308:3306"
    environment:
      MYSQL_ROOT_PASSWORD: cloudit
      MYSQL_PASSWORD: cloudit
      TZ: Asia/Seoul
    volumes:
      - ./mariadb/data:/data/mysql
      - /etc/mysql/my.cnf:/etc/my.cnf
```

아래와 같이 변경된 후에는 정상적으로 잘 실행되는 것을 확인할 수 있었다.
