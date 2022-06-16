# Refresh Token

## Refresh Token

Access Token(JWT)를 통한 인증 방식은 제 3자에게 탈취 당할 경우 보안에 취약하다는 단점이 있습니다. 만약 유효 기간을 짧게 할 경우 사용자는 그만큼 로그인을 자주 해서 새롭게 Token을 발급 받아야 하므로 불편합니다. 반면 유효 기간을 늘리면, 토큰을 탈취 당했을 때 더 오랜 기간 동안 무자비하게 사용될 수 있습니다. 

Refresh Token은 Access Token과 똑같은 형태의 JWT입니다. 처음 로그인을 완료했을 때 Access Token과 동시에 발급 되는 Refresh Token은 긴 유효 기간을 가지면서 Access Token이 만료 됐을 때 새로 발급해주는 열쇠가 됩니다. 

(Access Token을 탈취 당하면 정보가 유출되는 건 동일하지만, 짧은 유효 기간 안에만 사용이 가능하기 때문에 비교적 안전합니다)

(Refresh Token의 유효 기간이 만료됐다면, 사용자는 새로 로그인 해야 합니다. Refresh Token도 탈취 될 가능성이 있기 때문에 적절한 유효 기간 설정이 필요합니다 - 보통 2주로 많이 합니다)

## Access Token + Refresh Token 인증 과정

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/236050db-48f7-4c1e-98fb-60de2f4bf804/Untitled.png)

1. 사용자가 ID, PW를 통해 로그인
2. 서버에서는 회원 DB에서 값을 비교

3, 4. 로그인 완료 시 Access Token, Refresh Token 발급. 일반적으로 회원 DB에 Refresh Token 저장.

1. 사용자는 Refresh Token을 안전한 저장소에 저장 후, Access Token을 헤더에 실어 요청

6, 7. Access Token을 검증하여 이에 맞는 데이터 전송

1. 시간이 지나 Access Token이 만료됐다고 가정
2. 사용자는 이전과 동일하게 Access Token을 헤더에 실어 요청

10, 11. 서버는 Access Token이 만료 됨을 확인하고 권한 없음을 신호로 보냄

(*Access Token이 만료 될 때마다 계속 과정 9~11을 거칠 필요 없습니다.* 

*사용자(프론트엔드)에서 Access Token의 Payload를 통해 유효 기간을 알 수 있습니다. 따라서 프론트엔드 단에서 API 요청 전 토큰이 만료 된 경우 바로 재발급 요청을 할 수도 있습니다.)*

1. 사용자는 Refresh Token과 Access Token을 함께 서버로 보냄
2. 서버는 받은 Access Token이 조작되지 않았는지 확인한 후, Refresh Token과 사용자의 DB에 저장되어 있던 Refresh Token을 비교. Token이 동일하고 유효 기간이 지나지 않았다면 새로운 Access Token 발급
3. 서버는 새로운 Access Token을 헤더에 실어 다시 API 요청

## Refresh Token 장단점

**장점**

- 기존 Access Token만 있을 때보다 안전하다

**단점**

- 구현이 복잡하다. 검증 프로세스가 길기 때문에 자연스레 구현하기 힘들어졌다.
- Access Token이 만료될 때마다 새롭게 발급하는 과정에서 생기는 HTTP 요청 횟수가 많다. 이는 서버의 자원 낭비로 귀결 된다.
