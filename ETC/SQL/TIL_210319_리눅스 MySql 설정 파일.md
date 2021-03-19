# 리눅스 MySql 설정 파일

#### mysql 설정 파일

```
/etc/my.cnf : MariDB의 주 설정 파일. 파일 내용은 /etc/my.cnf.d/ 폴더의 파일들을 include 하는 것 뿐

/etc/my.cnf.d : 이 폴더 안에 모든 .cnf 파일들이 실제 설정 파일들이며, 이 파일들이 my.cnf 파일에서 include 된다

/etc/my.cnf.d/server.cnf : 서버의 실질적인 설정 파일. 서버의 characterset 설정을 여기서 하면 된다.

/etc/my.cnf.d/mysql-clients.cnf : mysql, mysqldump 같은 client app 들을 위한 설정 파일
```

<img src="https://t1.daumcdn.net/cfile/tistory/99DBFB355EC4DF3802" alt="img" style="zoom:80%;" />