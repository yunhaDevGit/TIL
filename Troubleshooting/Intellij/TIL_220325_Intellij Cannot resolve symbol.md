# Intellij ‘Cannot resolve symbol ‘~’’

`Cannot resolve symbol '~'`라는 에러 메시지와 함께 import가 안되는 문제 발생

그동안 종종 Intellij를 사용하면서 발생했던 에러기 때문에 별로 어렵지 않게 해결할 줄 알았다..

![image](https://user-images.githubusercontent.com/74949294/160094376-ab4afb33-29c2-4664-a64b-f400170612a3.png)

![image](https://user-images.githubusercontent.com/74949294/160094395-1857500a-387e-4a3b-ab31-3c9c0335cb37.png)

![image](https://user-images.githubusercontent.com/74949294/160094419-875b1a95-fb7f-44e6-8448-54d90e56ff05.png)

build.gradle도 잘 작성해주었고 dependency도 제대로 추가 된 것을 확인할 수 있었는데 import만 안되는 문제였다.

![image](https://user-images.githubusercontent.com/74949294/160094435-53d484ec-c725-4bb6-8a0c-729c5a8219fa.png)

해결 방법이 조금 어이없었지만 혹시나 나중에 똑같은 상황을 겪거나 다른 사람들이 좀 더 빠르게 해결 하길 바라는 마음에 정리해둔다.

## 해결 방법

아래의 순서대로 시도 해보았다. 

### 1. Rebuild

우측 `Gradle` Tool Tab의 **project > Tasks > build > clean, build** 실행

Mapstruct의 경우 import는 여전히 되지 않았지만 정상적으로 build는 되었기 때문에 빨간줄은 무시한 채로 계속 해서 개발을 하였다. 

![image](https://user-images.githubusercontent.com/74949294/160094469-ac873db9-3f5c-40a0-9792-1b8005b81d2f.png)

### 2. Invalid Cache

상단 메뉴바 **File > Invalid Cache** 

IDE 캐시를 날린 후 IDE가 재실행 된다. 

웬만한 문제는 Invalid Cache를 통해 해결 가능하다. 

![image](https://user-images.githubusercontent.com/74949294/160094501-33ebf3e7-8b60-498e-825c-dbd87d889827.png)

![image](https://user-images.githubusercontent.com/74949294/160094527-f3b3fb52-22e1-478f-9da2-f1cd1ea5352d.png)

그럼에도 불구하고 여전히 해결이 안되면,,,다음 방법으로 넘어간다 😹

### 3. Refresh Gradle Dependencies

전체 프로젝트에서 우클릭 후에 Refresh Gradle Dependencies를 누르면 된다.

다른 분들은 이 방법으로 해결됐다는데 여전히 내 것만 안되는 이유는 뭘까??ㅎㅎ

![image](https://user-images.githubusercontent.com/74949294/160094560-1fd01049-eb9d-40ff-8146-2ab8ee67e911.png)

### 4. Gradle 빌드 설정을 IntelliJ IDEA로 변경

**File > Settings > Build, Execution, Deployment > Build Tool > Gradle > Build and Run** 에서 설정을 모두 `IntelliJ IDEA`로 변경해준다.

여기서 나는 Gradle로 설정 되어있어서 약간의 희망을 품었지만....여전히 빨간줄ㅎㅎ

![image](https://user-images.githubusercontent.com/74949294/160094594-8052db2d-1462-4acb-9ac4-f8ffb75b7bcd.png)

### 5. IntellJ 업데이트

대망의 마지막 방법!!

IntelliJ 문제는 확실하니까 IDE를 최신버전으로 업데이트 해보지 뭐,,라는 생각으로 업데이트를 했는데.... 😡

~~(IDE 문제인지 확인하기 위해 일단 push 한 후에 다른 분한테 부탁해서 확인해봤는데 다른 분 IntelliJ에서는 import가 아주 잘 되는 것을 확인 했었다ㅎㅎ)~~

최신 버전으로 업데이트 하니 정말 정말 잘 동작했다^^

![image](https://user-images.githubusercontent.com/74949294/160094618-2c3efd48-8a80-4dc2-886a-51416dc9cf51.png)

다 해봤는데 안되면 그냥 IntelliJ 문제니까 IntelliJ를 꼭 업데이트 해보시길..
