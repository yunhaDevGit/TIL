# loadbalancer https 테스트

*loadbalancer에 https 기능을 추가한 후 해당 기능 테스트*



#### host

```
$ mkdir https-test
$ cd https-test
```



private key 생성

```
# openssl genrsa -out [파일명] 2048
$ openssl genrsa -out private.key 2048
```



서버 인증 요청서 생성

```
$ openssl req -x509 -new -nodes -key private.key -sha256 -days 365 -out privateCACert.pem
```



파일 생성

```
$ vi httpTest.pem
private.key 내용 + privateCACert.pem 내용 작성 후 저장
```



### VM

```
# 방화벽 확인
$ getenforce // selinux 확인
$ systemctl stop firewalld

# 웹 서버 데몬 설치
$ yum install httpd
$ systemctl start httpd

# ssl 모듈 설치
$ yum install mod_ssl
# ssl 파일 설정
$ vim /etc/httpd/conf.d/ssl.conf
-> #DocumentRoot "/var/www/html" 주석 해제
-> SSLCertificateFile /etc/pki/tls/certs/privateCACert.pem
-> SSLCertificateKeyFile /etc/pki/tls/private/private.key
	// 해당 경로에 키, 인증서 파일 넣은 후 경로 지정
	
# 웹서버 데몬 재시작
$ systemctl restart httpd

# 서비스 상태와 포트 점검
$ netstat -natlp | grep httpd
$ lsof -i rcp:443
```

![img](https://lh5.googleusercontent.com/ezhyg9J-VgTjiYEzufUMvNdQC1uWvKZJBy6ROZ6Gp9qxuDVP7_gaJRidaBE7QGOPS3rScaSAj_QS_Ey8epK7hzNfJ8PtJbn33IP-MNWIxZF2efu4LH_DZgdma6xTZzzG6holLeVh)

![img](https://lh6.googleusercontent.com/w77SjQQucHj9jHE0yyEgDT3KDcVoHTPPo1os7go-Fp3Mh8qKLAFPt3Z1_KkZllbrQUfmPyQBjNdHBeoNlvGDJFUXKcWBvIw1wHdW-OToUSyX5W4WVFnhi3kMZWyT3j6D-AFLe-dy)



### loadbalancer 생성

1. 포트 - unknown 포트를 제외한 포트 선택

   > 처음 테스트 할 때, 80 port를 이미 사용중이어서 22 port로 로드밸런서를 생성했었다. 근데 왜 이렇게 하면 안되냐고?
   >
   > ssh의 기본 포트가 22인데 서버에서 ssh service가 동작중이라고 하자. 그러면 서버 입장에서는 로드밸런서보다 ssh가 더 우선 순위가 된다. 
   >
   > 로드밸런서 생성할 때는 상관 없었겠지만, 실제로는 로드밸런서가 제대로 동작하지 않을 수 있다.
   >
   > ex ) 포트 : 55, 56, 111 ...

2. 프로토콜 : https

   파일 : private.key랑 privateCACert.pem 내용을 합친 httpsTest.pem 파일 업로드

3. 멤버 서버 포트 : 443