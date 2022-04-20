# Since Maven 3.8.1 http repositories are blocked. how to avoid https access

그동안 잘 build 되던 버전이 build error가 발생하는 문제가 생겼다. 

다른 분은 잘 build가 되는데 나만 안되길래 원인을 찾아보니 maven 3.8.1 버전이 나오면서 http에 대한 외부 연결을 막는 설정이 default로 변경되어 발생한 문제였다. 

[https://maven.apache.org/docs/3.8.1/release-notes.html](https://maven.apache.org/docs/3.8.1/release-notes.html)

얼마전 Mapstruct import 문제로 Intellj 최신 버전으로 재설치 하면서 maven 버전이 같이 업그레이드 돼서 발생한 문제인 것 같다. 

프로젝트 배포를 위해 `mvn clean package` 명령 실행 시 maven이 http uri를 block 하기에 내부망에 존재하는 http에 접속할 수 없어 `failed to collect dependencies ... [maven-default-http-blocker] ...`라는 error가 나오면서 build를 실패했다. 

이를 해결하기 위해 http repository의 내부망에 ssl 인증서를 등록하여 https로 변형 시켜 주는 방법과 maven settings.xml을 수정하여 http를 무력화 시키는 방법이 있는데 두 번째 방법을 사용했다.

1. **`settings.xml` 파일을 수정**

파일을 열고 `maven-default-http-blocker` 설정을 주석 또는 삭제한다. 

![image](https://user-images.githubusercontent.com/74949294/164188876-4e054803-05fe-4e82-bc89-5747d0be11e4.png)

![image](https://user-images.githubusercontent.com/74949294/164188914-7556f74f-0d24-4b7c-bd37-7a158493f9cd.png)

![image](https://user-images.githubusercontent.com/74949294/164188956-a8110e11-e25e-4038-b71e-b797c95fd0f4.png)

![image](https://user-images.githubusercontent.com/74949294/164188983-11f3bab2-4368-4f80-9f52-dd7157664bf2.png)

```xml
<!-- <mirror>
  <id>maven-default-http-blocker</id>
  <mirrorOf>external:http:*</mirrorOf>
  <name>Pseudo repository to mirror external repositories initially using HTTP.</name>
  <url>http://0.0.0.0/</url>
</mirror> -->
```

1. IntelliJ에서 clean > install 수행

![image](https://user-images.githubusercontent.com/74949294/164189016-f2b3ebc0-3ddf-4b7c-8196-04773116ad29.png)

[https://jjam89.tistory.com/201?category=903879](https://jjam89.tistory.com/201?category=903879)

[https://stackoverflow.com/questions/67001968/how-to-disable-maven-blocking-external-http-repositores](https://stackoverflow.com/questions/67001968/how-to-disable-maven-blocking-external-http-repositores)
