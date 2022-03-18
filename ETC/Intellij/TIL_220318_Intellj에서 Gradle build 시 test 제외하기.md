# Gradle test 제외하고 build 하기

나는 test code를 작성하지 않았는데 Gradle에서 계속해서 자동으로 test도 함께 build가 되어 에러가 발생했다. 

![image](https://user-images.githubusercontent.com/74949294/158960545-fdb9ff85-99cf-4bd6-8225-926f69ec9cd6.png)

maven build tool의 경우 아래에서 하늘색 원의 번개 모양 버튼 `Toogle 'Skip Tests' mode`을 누르면 자동으로 test를 제외하고 build를 하는데 gradle은 그런 버튼이 따로 없었다. 

maven toolbar

![image](https://user-images.githubusercontent.com/74949294/158960575-eda1ef2f-9896-49a9-a1f9-5ccab7531e73.png)


gradle toolbar

![image](https://user-images.githubusercontent.com/74949294/158960595-8f02e13e-5034-4984-927d-225f617899aa.png)


IntellJ에서 Gradle Build Tool을 사용할 경우 Test 제외하는 방법은 다음과 같다.

1. 실행 버튼 옆의 `Select Run/Debug Configuration`에서 `Edit Configurations...`를 선택
    
    ![image](https://user-images.githubusercontent.com/74949294/158960616-3895c183-e4e7-4e3f-b4df-d88b4fdd7364.png)
    
2. `+` 버튼을 누른 후 Gradle을 선택
    
    ![image](https://user-images.githubusercontent.com/74949294/158960787-0a71e202-c282-451f-a036-764b988d756a.png)
    
3. Configuration 이름과 Gradle Poject를 선택 후에 실행하고자 하는 Task와 제외할 Task를 작성해준다.
    
    clean, build 작업을 하고 싶고 test는 제외하고 싶기 때문에 `-x test`를 추가로 작성해주었다. 
    

![image](https://user-images.githubusercontent.com/74949294/158960754-58940d04-31fa-4175-8261-9abc089dd3ac.png)

4. 위와 같이 설정을 해주었더니 정상적으로 build 되는 것을 확인 할 수 있었다.

![image](https://user-images.githubusercontent.com/74949294/158960844-d4ee068a-7bf1-4539-ba7a-b97bef314a61.png)
