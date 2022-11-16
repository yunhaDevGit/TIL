# Openstack API 요청

## Openstack Token 발급

******요청******

```bash
curl -i \
  -H "Content-Type: application/json" \
  -d '
{ "auth": {
    "identity": {
      "methods": ["password"],
      "password": {
        "user": {
          "name": "<<id>>",
          "domain": { "id": "default" },
          "password": "<<password>>"
        }
      }
    }
  }
}' \
  "http://<<serverIp>>:5000/v3/auth/tokens" ; echo
```

 아래의 빨간 표시된 부분을 수정하여 요청

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/19a067ce-2a37-4f27-8f83-b5c2bbfab9bc/Untitled.png)

**********응답**********

![image](https://user-images.githubusercontent.com/74949294/202164538-a42f9156-f460-4ecc-b37f-081f307f6727.png)

위와 같이 넘어오는 응답 값 중 `x-subject-token` 값을 가지고 옵니다.

## API 요청 (하이퍼바이저 리스트 조회)

### API endpoint 확인

![image](https://user-images.githubusercontent.com/74949294/202164713-2fa41657-ea8d-4f1d-979e-d9dba39c4be7.png)

프로젝트 > API 액세스의 Service Endpoint 확인

### 하이퍼바이저 리스트 조회 API 요청

```bash
curl s -H "X-Auth-Token: <<x-subject-token>>" <<API Service Endpoint>>/os-hypervisors/detail
```

```bash
// **example**

curl s -H "X-Auth-Token: gAAAAABjdK2E1XpqzUgy116sM21DEYOmc8tesCDuH-W9dEt4jKesusSzb1XW8w5dH5V8E7AhKa416aZr7JuXjd2nx9VY_cyESS9rK6rZ1X0ZZf5OtEijrtd6PC_0Enedu_iXc9u41KJ-1GaVbsUadTlCEPB04izJ3jbv-YyHuwlGMctd_cNpWuI" [http://192.168.130.130:8774/v2.1/os-hypervisors/detail](http://192.168.130.130:8774/v2.1/os-hypervisors/detail)
```

**응답 결과**

![image](https://user-images.githubusercontent.com/74949294/202164817-6525cd00-2dbe-4ecb-adb0-667bbed3fb57.png)

➕ 만약 아래와 같이 403이 나오는 경우 오픈스택 정책 문제로 API 요청이 어렵습니다.

![image](https://user-images.githubusercontent.com/74949294/202164843-c6b0c38f-a623-4090-a9f7-820c8b57593d.png)
