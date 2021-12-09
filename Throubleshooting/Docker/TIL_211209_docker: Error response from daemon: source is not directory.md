## docker: Error response from daemon: source is not directory.

Docker로 telegraf를 run 명령어로 돌릴 때 /docker/telegraf/telegraf.conf 파일을 telegraf container에 적용(?) 하기 위한 볼륨과 
influxdb 컨테이너와의 네트워크 연동을 위한 docker bridge 설정을 함께 실행해주었다. 

```yml
# cd /docker/telegraf
$ docker run -d --name=telegraf -v telegraf.conf:/etc/telegraf/telegraf.conf --network=influxdb-telegraf telegraf
```

그러나 계속 아래와 같은 에러 메시지와 함께 run 명령이 안뜨는 것 아닌가...ㅡㅡ
```
docker: Error response from daemon: source is not directory.
```

directory를 찾을 수 없다기에 `-v telegraf.conf~`를 `-v ./telegraf.conf~`로 변경하여 주었더니
```
docker: Error response from daemon: create ./telegraf.conf: "./telegraf.conf" includes invalid characters for a local volume name, only "[a-zA-Z0-9][a-zA-Z0-9_.-]" are allowed. If you intended to pass a host directory, use absolute path.
```
와 같은 메시지가 나왔다. 


그렇게 조금 헤매다 절대 경로를 써주었더니....정상적으로 동작했다^^
```
docker run -d --name=telegraf -v /root/docker/telegraf/telegraf.conf:/etc/telegraf/telegraf.conf --network=influxdb-telegraf telegraf
```


