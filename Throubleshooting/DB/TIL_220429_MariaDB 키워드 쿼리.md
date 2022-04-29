# MariaDB 키워드

key, value 컬럼으로 구성된 config 테이블의 value 값을 업데이트 하기 위해 `update`쿼리를 작성하여 실행했는데 쿼리 요청은 ok 떴지만 실제 value가 변경되지 않았다. 

```sql
update config set value='[192.168.120.6,192.168.120.7]' where key='MANAGEMENT_IP';
```

위와 같은 쿼리를 날렸는데 계속해서 업데이트가 정상적으로 안되는 문제였다. 

알고 보니 key는 MariaDB의 예약어여서 ``를 사용하여 예약어가 아님을 나타내야 했다.

```sql
update config set value='[192.168.120.6,192.168.120.7]' where `key`='MANAGEMENT_IP';
```

위와 같이 쿼리문을 변경해주니 정상적으로 update가 처리되었다.

[https://mariadb.com/kb/en/reserved-words/](https://mariadb.com/kb/en/reserved-words/)
