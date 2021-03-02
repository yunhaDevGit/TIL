# Intellij 한글 깨짐 (인코딩 에러)

인텔리제이에서 한글 깨지는 문제 해결 방법

각각의 단계를 실행 후 인텔리제이 재시작 해보기!



##### 1. File -> Setting -> Editor -> File Encodings

<img src="C:\Users\The Jeong\AppData\Roaming\Typora\typora-user-images\image-20210302194609934.png" alt="image-20210302194609934" style="zoom:80%;" />

![image-20210302194132534](C:\Users\The Jeong\AppData\Roaming\Typora\typora-user-images\image-20210302194132534.png)

- Global Encoding, Project Encoding, Default encoding for properties files를 모두 UTF-8로 설정해준다.
- Transparent native-to-ascii conversion은 선택
  - 프로퍼티 파일의 한글이 모두 유니코드로 인코딩 되어 깨져 있더라도 한글로 변환해준다.



##### 2. VM Option 값 변경

*Shift + Shift*  ->  vm 검색

<img src="C:\Users\The Jeong\AppData\Roaming\Typora\typora-user-images\image-20210302194818987.png" alt="image-20210302194818987" style="zoom:80%;" />

<img src="C:\Users\The Jeong\AppData\Roaming\Typora\typora-user-images\image-20210302195004061.png" alt="image-20210302195004061" style="zoom:80%;" />

추가

```
-Dfile.encoding=UTF-8
```



##### 3. build.gradle 설정

<img src="C:\Users\The Jeong\AppData\Roaming\Typora\typora-user-images\image-20210302195555140.png" alt="image-20210302195555140" style="zoom:80%;" />

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

<img src="C:\Users\The Jeong\AppData\Roaming\Typora\typora-user-images\image-20210302195106340.png" alt="image-20210302195106340" style="zoom:80%;" />

<img src="C:\Users\The Jeong\AppData\Roaming\Typora\typora-user-images\image-20210302195304795.png" alt="image-20210302195304795" style="zoom:80%;" />