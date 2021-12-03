# host의 system log file 종류

### Linux Log File

- 대부분의 로그 파일 저장 경로는  `/var/log/`이다.

### 로그 관련 데몬

1. syslogd
 - 커널과 여러 가지 시스템 프로그램은 각종 에러와 경고 메시지, 기타 일반 메시지들을 출력하는데 syslogd는 메시지들을 파일로 기록하는 데몬

2. klogd
 - 부팅 후에 부팅과 관련된 메시지를 보기 위해서는 dmesg 명령어를 사용한다.
 - 시스템에는 부팅과 관련된 메시지를 포함하여 커널 관련 메시지를 기록하는 데몬

### 로그 파일 종류

1. /var/log/messages

 - 시스템에 문제가 생겼을 때 가장 먼저 찾아보는 log file
 - syslog.conf에서 로그를 남기지 않는 것으로 지정된 내용을 제외한 모든 항목이 기록된다 

2. /var/log/secure

 - 사용자 접속 정보 기록
 - ssh, 텔넷 등으로 시스템에 접속한 내용이 기록되며, 접속 일시, 사용자 명, 시스템의 IP가 기록된다 

3. /var/log/maillog

 - 메일 송,수신 내용이 기록된다

4. /var/log/cron

 -  cron이 실행된 것들에 대한 정보가 기록된다

5. /var/log/boot.log

 - 서비스 데몬들의 부트에 관련된 정보가 저장된다
 - 각 데몬들의 시작/종료/실패 내용이 기록된다
 - 부팅 관련 내용 뿐만 아니라 종료 (shutdown) 시의 내용도 기록된다

6. /var/log/dmesg

 - 부팅 시의 시스템 로그가 기록된다.
 - dmesg 명령어를 통해 출력되는 내용이 위 파일에 기록 된다

7. /var/log/wtmp

 - 최근의 접속 사항이 기록되는 파일
 - last 명령어를 통해 출력 되는 내용이 기록 된다

8. /var/log/lastlog

 - 각 사용자의 마지막 로그인 내용이 기록된다
 - 직접 파일을 볼 수는 없고, lastlog 명령어를 통해 확인이 가능하다

9. /var/log/httpd/error_log

 - Apache 서비스 데몬의 에러 사항에 대한 내용이 기록 된다.

[https://blog.naver.com/kdi0373/220522832069](https://blog.naver.com/kdi0373/220522832069)

[https://harryp.tistory.com/880](https://harryp.tistory.com/880)
