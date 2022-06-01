# .iml (class path) 달라서 build 안되는 이슈

프로젝트를 진행하면서 다른 브랜치를 pull 받은 뒤에 작업을 하다가 다시 다른 branch로 이동해서
maven clean, install을 했는데 계속해서 build가 안되는 문제가 있었다.
나랑 동일한 브랜치를 pull 받으신 다른 분들은 정상적으로 잘 build가 됐는데 나만 안되는게 너무 이상했다..
심지어 다른 브랜치 갔다 오기 전까지는 잘 동작했음,,ㅡㅡ

```bash
nl.captcha:SimpleCaptcha:pom:1.2.1 failed to transfer from [https://repository.jboss.org/nexus/content/repositories/thirdparty-uploads]
(https://repository.jboss.org/nexus/content/repositories/thirdparty-uploads) during a previous attempt. This failure was cached in the 
local repository and resolution is not reattempted until the update interval of thirdparty-uploads has elapsed or updates are forced. 
Original error: Could not transfer artifact nl.captcha:SimpleCaptcha:pom:1.2.1 from/to thirdparty-uploads 
([https://repository.jboss.org/nexus/content/repositories/thirdparty-uploads](https://repository.jboss.org/nexus/content/repositories/thirdparty-uploads)):
 transfer failed for [https://repository.jboss.org/nexus/content/repositories/thirdparty-uploads/nl/captcha/SimpleCaptcha/1.2.1/SimpleCaptcha-1.2.1.pom]
(https://repository.jboss.org/nexus/content/repositories/thirdparty-uploads/nl/captcha/SimpleCaptcha/1.2.1/SimpleCaptcha-1.2.1.pom), 
status: 503 Service Unavailable

Try run Maven import with -U flag (force update snapshots)
```

해당 에러에 대해 구글링을 해보았지만 전부 Intellij Invalid Cache를 하라고 했지만 나는 아래의 행동을 이미 다 취한 상태였다.

1. Maven Clean, Install
2. Intellij Invalid Cache
3. /.m2 repository 제거
4. maven reload

이 모든 걸 다양한 방법으로 시도해 보았지만 계속해서 동일한 이슈가 발생했다.
다른 분들과 동일한 브랜치를 동작하는데 안되는게 계속해서 이해가 안되는 상태로 찾아보았는데 찾다가File > Settings > Build, Execution, Deployment > Build Tools > Maven > Repositories를 보게 되었다.

해당 경로에는 Maven Repositories에서 받아오는 URL 경로가 적혀있었는데 해당 경로에 위의 에러에 나오는 경로가 없는게 이상하다고 여겨 다른 분의 IntelliJ에서 확인해보았다. 역시나 나랑은 많이 달랐고, 다른 분의 Intellj의 Maven Repositories URL 목록에 위의 경로가 포함되어 있었다.

여기서 희망이 생겨서 계속해서 찾다가

Settings.xml의 우선순위가 아래와 같다.
global > user > project

global있으면 나머지 못읽어옴

find / -name "settings.xml"
