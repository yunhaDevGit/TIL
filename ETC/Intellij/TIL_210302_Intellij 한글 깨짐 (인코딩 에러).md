# Intellij 한글 깨짐 (인코딩 에러)

인텔리제이에서 한글 깨지는 문제 해결 방법

각각의 단계를 실행 후 인텔리제이 재시작 해보기!



##### 1. File -> Setting -> Editor -> File Encodings

![intellij_인코딩0](https://user-images.githubusercontent.com/48197531/111123731-0980ac80-85b3-11eb-9c10-b39861f3c33d.png)

![intellij_인코딩](https://user-images.githubusercontent.com/48197531/111123714-0685bc00-85b3-11eb-9b28-20ff6a863ba6.png)

- Global Encoding, Project Encoding, Default encoding for properties files를 모두 UTF-8로 설정해준다.
- Transparent native-to-ascii conversion은 선택
  - 프로퍼티 파일의 한글이 모두 유니코드로 인코딩 되어 깨져 있더라도 한글로 변환해준다.



##### 2. VM Option 값 변경

*Shift + Shift*  ->  vm 검색


![intellij_인코딩1](https://user-images.githubusercontent.com/48197531/111123469-b575c800-85b2-11eb-88e6-e016b5be6555.png)

![intellij_인코딩2](https://user-images.githubusercontent.com/48197531/111123471-b73f8b80-85b2-11eb-826f-a39084f8abb2.png)


추가

```
-Dfile.encoding=UTF-8
```



##### 3. build.gradle 설정


![intellij_인코딩3](https://user-images.githubusercontent.com/48197531/111123489-bd356c80-85b2-11eb-9d46-75270ead6dfd.png)


추가

```
sourceCompatibility = 1.8
targetCompatibility = 1.8
compileJava.options.encoding = 'UTF-8'
tasks.withType(JavaCompile) {
    options.encoding = 'UTF-8'
}
```



##### 4. Edit Configurations...

![intellij_인코딩4](https://user-images.githubusercontent.com/48197531/111123507-c1618a00-85b2-11eb-87bc-c26249bd91c5.png)

![intellij_인코딩5](https://user-images.githubusercontent.com/48197531/111123524-c6263e00-85b2-11eb-9048-45cf088c03a6.png)
