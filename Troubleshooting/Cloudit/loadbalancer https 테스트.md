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





### 테스트

##### 브라우저에서 요청

```
https://[로드밸런서 ip]:port
```



##### 터미널에서 요청

```
curl https://[로드밸런서 ip]:port --insecure
```

>--insecure (-k)
>
>curl은 기본적으로 https 사이트의 ssl 인증서를 검증한다. 인증 기간의 인증서 목록이 없거나, 모르는 기관에서 발급한 인증서의 경우 인증서 검증 에러를 발생시키고 동작을 중지한다.
>
>ex) curl https://192.168.120.132:55 로 요청시 아래와 같은 에러메시지가 발생한다.
>
>​     *curl: (60) Peer's certificate issuer has been marked as not trusted by the user.*
>
>​			또는
>
>​     *cURL error 60: SSL certificate problem: unable to get local issuer certificate (see http://curl.haxx.se/libcurl/c/libcurl-errors.html)*
>
>
>
>해결 방법
>
>1. 검증을 하지 않는 옵션을 준다. -k / --insecure
>   - curl https://192.168.120.132:56 --insecure
>2. 해당 인증서를 발급한 기관을 신뢰하는 인증 기관 목록에 추가한다
>   - 해당 내용은 블로그 참고
>   - https://www.lesstif.com/gitbook/curl-ca-cert-15892500.html





참조

https://jdh5202.tistory.com/601

https://blog.lael.be/post/7147

https://www.lesstif.com/gitbook/curl-ca-cert-15892500.html