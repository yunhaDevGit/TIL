# Tomcat에 directory 배포하기

설치한 Tomcat에 directory 자체를 배포하는 방법에 대해 알아보도록 하겠습니다. 

설치하는 방법은 이 포스터에 작성하였으니 설치가 안되었을 경우 해당 포스팅에서 확인해주시길 바랍니다. 

우선  배포할 디렉토리를 설치된 Tomcat의 `webapp` 폴더에 넣어줍니다. 해당 예제에서는 `test` 디렉토리를 배포하도록 하겠습니다.

필자의 경우 `/usr/local/tomcat8/` 경로에 톰캣을 설치하였습니다. 

![image](https://user-images.githubusercontent.com/74949294/195785393-291e6e0a-a502-427e-90d4-d224ddca80b4.png)

### 1. server.xml 수정

톰캣이 설치된 디렉토리의 `{톰캣이 설치된 경로}/conf` 폴더 안에 있는 `server.xml` 맨 아래에 아래와 같이 Context 경로를 설정합니다.

```xml
<Context path="/context path 명" docBase="webapp 아래 배포될 디렉토리 명"> </Context>
```

![image](https://user-images.githubusercontent.com/74949294/195785409-d25a3b3b-1108-4cf1-a7c9-07a2dc6507d1.png)

### 2. 배포할 디렉토리 이동

배포하길 원하는 디렉토리를 `{톰캣이 설치된 경로}/webapps` 아래로 이동합니다.

![image](https://user-images.githubusercontent.com/74949294/195785440-f9ffb155-e716-4196-b23a-c63fc16602d9.png)

### 3. 톰캣 재시작

```bash
$ systemctl restart tomcat
```

톰캣 매니저 화면을 확인하면 `/test` 디렉토리가 정상적으로 배포 된 것을 확인할 수 있습니다.

![image](https://user-images.githubusercontent.com/74949294/195785496-b9644964-4b4c-49cc-b095-6e83906fd353.png)

`[http://localhost:8080/test/test.html](http://localhost:8080/test/test.html)` 접속 시 정상적으로 호출되는 것을 확인할 수 있습니다.

![image](https://user-images.githubusercontent.com/74949294/195785507-d0ecd976-b920-44ab-b390-db9ad68d198c.png)

![image](https://user-images.githubusercontent.com/74949294/195785515-bfdcd7db-5a99-4e21-aa9d-9f8827036be9.png)

