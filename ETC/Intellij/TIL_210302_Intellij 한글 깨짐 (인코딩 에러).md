# Intellij 한글 깨짐 (인코딩 에러)

인텔리제이에서 한글 깨지는 문제 해결 방법

각각의 단계를 실행 후 인텔리제이 재시작 해보기!



##### 1. File -> Setting -> Editor -> File Encodings

![intellij_인코딩0](https://user-images.githubusercontent.com/48197531/111124423-d854ac00-85b3-11eb-80dc-a1b94f64c779.png)

![intellij_인코딩](https://user-images.githubusercontent.com/48197531/111124429-d985d900-85b3-11eb-9a65-5e96b834c501.png)


- Global Encoding, Project Encoding, Default encoding for properties files를 모두 UTF-8로 설정해준다.
- Transparent native-to-ascii conversion은 선택
  - 프로퍼티 파일의 한글이 모두 유니코드로 인코딩 되어 깨져 있더라도 한글로 변환해준다.



##### 2. VM Option 값 변경

*Shift + Shift*  ->  vm 검색

![intellij_인코딩1](https://user-images.githubusercontent.com/48197531/111124464-e5719b00-85b3-11eb-88f2-52b95e09d91e.png)

![intellij_인코딩2](https://user-images.githubusercontent.com/48197531/111124472-e6a2c800-85b3-11eb-9bbd-7d6509820b00.png)


추가

```
-Dfile.encoding=UTF-8
```



##### 3. build.gradle 설정
![intellij_인코딩3](https://user-images.githubusercontent.com/48197531/111124494-eb677c00-85b3-11eb-87a5-09a9a3cba5d8.png)


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

![intellij_인코딩4](https://user-images.githubusercontent.com/48197531/111124517-f0c4c680-85b3-11eb-8e59-a115159fdc8c.png)

![intellij_인코딩5](https://user-images.githubusercontent.com/48197531/111124527-f3bfb700-85b3-11eb-8241-21b878a5acde.png)