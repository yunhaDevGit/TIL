# Slow Query

Slow Query는 DBMS가 client로부터 요청 받은 query를 수행할 때 일정 시간 이상 수행되지 못한 query입니다. 

즉 길어야 1~2초 걸리는 db 쿼리가 예상보다 오래 걸리는 경우를 뜻합니다.

이와 같은 예외 상황이 발생하게 되면 로그를 확인하면 되는데 로그 관련 설정을 `my.cnf`나 `명령어`를 통해 할 수 있습니다.

### my.cnf 설정

mysql의 경우에 slow query는 my.cnf 파일 내에서 설정합니다.

my.cnf의 경로는 주로 /etc/my.cnf를 사용합니다. 

*my.cnf로 수정할 경우 mysql 서비스를 재시작 해야 합니다.* 

| 항목 | my.cnf 추가 내용 |
| --- | --- |
| 해당 값(초) 이상의 로그 기록 | long_quert_time = 3 |
| 슬로우 쿼리 활성화 여부
(0 = 비활성 / 1 = 활성) | slow_query_log = 1 |
| 슬로우 쿼리 로그 파일 경로 | slow_query_log_file = /var/log/mysql/slow_queries.log |
| index를 사용하지 않는 쿼리 기록 여부 | log_queries_not_using_indexes = on |

### mysql 명령어

| 항목 | my.cnf 추가 내용 |
| --- | --- |
| 해당 값(초) 이상의 로그 기록 | mysql > set glogbal long_quert_time = 3 |
| 슬로우 쿼리 활성화 여부
(0 = 비활성 / 1 = 활성) | mysql > set global slow_query_log = 1 |
| 슬로우 쿼리 로그 파일 경로 | mysql > set global slow_query_log_file = “/var/log/mysql/slow_queries.log” |
| index를 사용하지 않는 쿼리 기록 여부 | mysql > set global log_queries_not_using_indexes = on |

```bash
Time = 쿼리요청시각
Query_time = 쿼리 수행 시간
Lock_time = 락이 걸린 시간
Rows_sent = 쿼리 결과 row 수
Rows_examined = 쿼리 대상 row 수
```

