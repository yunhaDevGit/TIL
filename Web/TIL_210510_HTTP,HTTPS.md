# HTTP / HTTPS

https, ssl은 정보 보호 수단이다.

인터넷에 나와 있는 예시를 따라하면 해당 수단을 적용하는 것은 어렵지 않지만, 어떠한 문제가 발생했을 때 해당 기술이 어떻게 동작하는지 근본적인 동작 원리를 이해하지 못한다면 그 문제를 해결하는데 큰 어려움이 있을 거다.

![img](https://lh6.googleusercontent.com/MCxKhrielXClVYdVZvL0ejMVuIJWECesHRvpqQ4PkAgX_ze_W7ond506WSBhJlQFCzNX44AeaJWxksVWdFDHqtDtPgW5KX2HWbXttUQ5v7n1BerYnoLmy-0cF_ASqPU2wQIPEAej)

네트워크 통신을 할 때, 계층적으로 layer라고 하는 것이 구성이 되어 있다.

ssl 위에 http, ftp, telnet 등을 위의 layer로 두고 있다.

ssl 프로토콜 위에서 동작하는 서비스 중 하나가 http이고, http가 ssl을 이용하게 되면 https가 된다.



#### HTTP(Hypertext Transfer Protocol)

- HTTP - 하이퍼텍스트(주로 HTML)를 교환하기 위한 통신 규약(프로토콜)
- 80 포트 사용
- Hypertext : 문서와 문서가 링크로 연결되어 있는 것
  (인터넷 상에서 색이 다른 글씨를 누르면 다른 페이시가 보이는 것을 종종 경험하는데, 이처럼 문서들이 서로 연결되어 있는 것을 하이퍼텍스트라고 한다)



#### HTTPS(Hypertext Transfer Protocol Secure)

- http에 데이터 암호화가 추가된 프로토콜
- 443 포트 사용
- 네트워크 상에서 중간에 제 3자가 정보를 볼 수 없도록 공개키 암호화를 지원



