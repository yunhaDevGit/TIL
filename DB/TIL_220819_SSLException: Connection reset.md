# Caused by: javax.net.ssl.SSLException: Connection reset

Application 실행 시 계속해서 다음과 같은 에러가 발생했다. 

실행 할 때 Application 설정 중에 SSL을 설정한 기억이 없는데….다른 원인인가 싶어서 한참을 찾다가 보니 역시 SSL 설정 때문이었다. 

```jsx

Caused by: javax.net.ssl.SSLException: Connection reset
	at sun.security.ssl.Alert.createSSLException(Alert.java:127) ~[?:1.8.0_272]
	at sun.security.ssl.TransportContext.fatal(TransportContext.java:324) ~[?:1.8.0_272]
	at sun.security.ssl.TransportContext.fatal(TransportContext.java:267) ~[?:1.8.0_272]
	at sun.security.ssl.TransportContext.fatal(TransportContext.java:262) ~[?:1.8.0_272]
	at sun.security.ssl.SSLTransport.decode(SSLTransport.java:135) ~[?:1.8.0_272]
	at sun.security.ssl.SSLSocketImpl.decode(SSLSocketImpl.java:1143) ~[?:1.8.0_272]
	at sun.security.ssl.SSLSocketImpl.readHandshakeRecord(SSLSocketImpl.java:1054) ~[?:1.8.0_272]
	at sun.security.ssl.SSLSocketImpl.startHandshake(SSLSocketImpl.java:394) ~[?:1.8.0_272]
	at com.mysql.cj.protocol.ExportControlled.performTlsHandshake(ExportControlled.java:316) ~[mysql-connector-java-8.0.17.jar:8.0.17]
	at com.mysql.cj.protocol.StandardSocketFactory.performTlsHandshake(StandardSocketFactory.java:188) ~[mysql-connector-java-8.0.17.jar:8.0.17]
	at com.mysql.cj.protocol.a.NativeSocketConnection.performTlsHandshake(NativeSocketConnection.java:99) ~[mysql-connector-java-8.0.17.jar:8.0.17]
	...
  at org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor$AutowiredFieldElement.inject(AutowiredAnnotationBeanPostProcessor.java:593) ~[spring-beans-5.1.8.RELEASE.jar:5.1.8.RELEASE]
	... 32 more
	Suppressed: java.net.SocketException: Broken pipe (Write failed)
		at java.net.SocketOutputStream.socketWrite0(Native Method) ~[?:1.8.0_272]
		at java.net.SocketOutputStream.socketWrite(SocketOutputStream.java:111) ~[?:1.8.0_272]
		at java.net.SocketOutputStream.write(SocketOutputStream.java:155) ~[?:1.8.0_272]
		at sun.security.ssl.SSLSocketOutputRecord.encodeAlert(SSLSocketOutputRecord.java:81) ~[?:1.8.0_272]
		at sun.security.ssl.TransportContext.fatal(TransportContext.java:355) ~[?:1.8.0_272]
		at sun.security.ssl.TransportContext.fatal(TransportContext.java:267) ~[?:1.8.0_272]
		at sun.security.ssl.TransportContext.fatal(TransportContext.java:262) ~[?:1.8.0_272]
		at sun.security.ssl.SSLTransport.decode(SSLTransport.java:135) ~[?:1.8.0_272]
		at sun.security.ssl.SSLSocketImpl.decode(SSLSocketImpl.java:1143) ~[?:1.8.0_272]
		at sun.security.ssl.SSLSocketImpl.readHandshakeRecord(SSLSocketImpl.java:1054) ~[?:1.8.0_272]
		at sun.security.ssl.SSLSocketImpl.startHandshake(SSLSocketImpl.java:394) ~[?:1.8.0_272]
		at com.mysql.cj.protocol.ExportControlled.performTlsHandshake(ExportControlled.java:316) ~[mysql-connector-java-8.0.17.jar:8.0.17]
		at com.mysql.cj.protocol.StandardSocketFactory.performTlsHandshake(StandardSocketFactory.java:188) ~[mysql-connector-java-8.0.17.jar:8.0.17]
		at com.mysql.cj.protocol.a.NativeSocketConnection.performTlsHandshake(NativeSocketConnection.java:99) ~[mysql-connector-java-8.0.17.jar:8.0.17]
	  ...
		at org.springframework.boot.devtools.restart.RestartLauncher.run(RestartLauncher.java:49) [spring-boot-devtools-2.1.6.RELEASE.jar:2.1.6.RELEASE]

Disconnected from the target VM, address: '127.0.0.1:57337', transport: 'socket'

Process finished with exit code 0
```

예전에 설정해두었던 SSL 설정을 새까맣게 까먹고 있었다. 

`/etc/my.cnf`를 보니 다음과 같이 설정 되어 있었다.

```bash
#
# This group is read both by the client and the server
# use it for options that affect everything
#
[client-server]
!includedir /etc/my.cnf.d
[mysqld]
unix_socket = OFF
ssl-ca=/etc/cloudit/.keystore/sscCACert.pem
ssl-cert=/etc/cloudit/.keystore/sscCACert.pem
ssl-key=/etc/cloudit/.keystore/sscKey.key
bind-address = 0.0.0.0
tls_version=TLSv1.2
[mariadb]
query_cache_size=0
binlog_format=ROW
default_storage_engine=innodb
innodb_autoinc_lock_mode=2

#
# include *.cnf from the config directory
#
#!includedir /etc/my.cnf.d
```

위의 설정 중 ssl 관련 설정을 모두 주석 해주었다. 

그랬더니,,,아주 잘 실행됨ㅎㅎ
