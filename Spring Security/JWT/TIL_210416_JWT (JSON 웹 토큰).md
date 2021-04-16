# JWT (JSON 웹 토큰)

- JSON 객체로 안전하게 어떤 정보를 전송하기 위한 방식
  - 정보 : 디지털 서명이 되어 있으므로 신뢰할 수 있다.
  - 서명의 용도로 사용
  - HMAC, RSA 방식으로 사용된다.
  - JWT를 암호화하여 당사자간에 비밀을 제공할 수 있지만 **JWT의 핵심은 서명된 토큰에 중점을 둔다**
    - 서명된 토큰 안에 클레임(요구사항)이 있는데 클레임의 무결성을 확인할 수 있게 한다



- JWT 언제 사용하나??



- JWT 구조
  - xxxxx.yyyyyy.zzzzzz
  - header
  - payload(정보)
  - signature(서명)



https://jwt.io/introduction