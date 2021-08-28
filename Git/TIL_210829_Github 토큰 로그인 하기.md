# Intellij에서 Github 토큰 로그인 하기

그동안 사용하던 Intellij 학생 라이센스가 만료되어 새로운 라이선스 발급 후 이 기회에 Intellij 버전도 업그레이드 했다.

그런데 갑자기 그동안 잘되던 git repository로의 push가 안됐다ㅠ

라이센스랑 Intellij 버전을 업그레이드 하는 과정에서 뭔가 잘못 설정되었나 싶어 여기저기 찾아봤는데 답이 안나왔다ㅠ

그러다 놓치고 있던 push failed 메시지를 확인하였다.

![img](https://blog.kakaocdn.net/dn/OVAFD/btrdpsqL71L/54MTubkiJCWg6S3EscPi81/img.png)

![img](https://blog.kakaocdn.net/dn/ozrN0/btrdqjfNKho/ecOURkvt41dzK1wK9An1U0/img.png)

push 시도할 때마다 계속해서 저 메시지가 찍혔지만 이해할 수가 없었다. 

왜냐면 분명 나는 File -> Settings ->Verstion Control -> Github 에서 Account를 token을 사용하여 인증하였기 때문이다.

그렇게 한참을 헤매다 결국 8월 13일부로 Github 정책이 바껴 **비밀번호 대신 토큰을 사용하게 되었고, 로컬 컴퓨터와 새로 생성한 토큰을 연결**해줘야 한다는 것을 알게 되었다.

하필 내 학생 라이센스 만료일이 8월 12일이어서 13일에 라이센스 갱신 한 후에 맞물려서 이 문제가 발생해서 원인을 찾는데 조금 더 헤맸던 것 같다😢

혹시 remote: Support for password authentication was removed on August 13, 2021. Please use a personal access token instead.

이런 에러 메시지와 함께 push가 안된다면 아래와 같이 로컬 컴퓨터 설정을 해주자!

token 발급부터 로컬 컴퓨터 셋팅까지 진행해보자~

1. Github 프로필을 누른 후 Settings를 선택한다

   ​

   ![img](https://blog.kakaocdn.net/dn/bT3tWn/btrdpEqMG1C/MnXTOFPocuzsGyIfw3uHDK/img.png)

   ​

2. 왼쪽 하단의 Developer settings를 선택한다

   ![img](https://blog.kakaocdn.net/dn/F0BCq/btrdtLh9DNi/rs7giXriKoACBPOcPs1OmK/img.png)

   ​

3.  Personal access tokens를 선택 후 Generate new token 버튼을 눌러 새로운 token을 발급 받는다

   ​

   ![img](https://blog.kakaocdn.net/dn/dT2JVE/btrdtKwMTEE/jV22N2jgkDcav0hYNXuyE0/img.png)

   ​

   이렇게 발급 받은 token은 다시 확인할 수 없으니 다른 곳에 잘 저장해둔다!!

   ​

4.  제어판 > 사용자 계정 > 자격 증명 관리 > Windows 자격 증명을 누른 후 일반 자격 증명의 Github 관련 자격 증명을 삭제 후 새로 설정해준다.

   ​

   ​

   ![img](https://blog.kakaocdn.net/dn/bn8OSB/btrdpqTRK7t/sjz33onJzY5K8h1r1Rckq0/img.png)

   ​

   일반 자격 증명 추가를 눌러 자격 증명을 추가할 수 있다.

   ​

   사용자 이름에는 본인의 계정을 적어주고, 암호에는 조금 전에 발급받은 token을 넣어주면 된다.

   ​

   ![img](https://blog.kakaocdn.net/dn/bNstiq/btrdtLh975F/aIbyRu9Yu47pglbw89HMtK/img.png)

   ​

   ​

5. 활성화 된 Github 사용자 계정 확인

   ​

   ```
   git config --global user.name﻿
   ```

   ​

   cmd, powershell 등에 해당 명령을 치면 활성화 된 Github 아이디를 확인 할 수 있다.

   ​

   원하는 계정으로 설정되었는지 확인 후 다시 Intellij로 가서 push를 하면 정상적으로 push가 되는 것을 확인 할 수 있다.

   ​



https://velog.io/@somprk/Git-8%EC%9B%94-13%EC%9D%BC%EB%B6%80%EB%A1%9C-%EB%B0%94%EB%80%90-%ED%86%A0%ED%81%B0-%EC%9D%B8%EC%A6%9D-%EB%A1%9C%EA%B7%B8%EC%9D%B8

