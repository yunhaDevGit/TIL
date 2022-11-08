FileOuputStream / FileWriter / BufferedWriter 이 세가지는 공통적으로 **파일에 텍스트를 저장하기 위한 용도로 사용**된다.

## FileOuputStream

FileOuputStream은 1byte 단위로 처리하며, 버퍼가 없어 ************write() 시 바로 디스크에 쓰기 위한 시스템 콜이 이뤄진다.  flush()나 close()를 만나지 않아도 1글자 마다 디스크에 접근하기 떄문에 효율이 가장 떨어진다.************ 1byte 단위로 전송하기 때문에 한글은 깨지며 영어 숫자 특수문자는 잘 된다. 

바로 다음에 오는 링크는 버퍼를 사용하지 않고 텍스트를 파일에 저장하는 FileOutputStream 을 설명한 그림이다.

[https://hajsoftutorial.com/java/wp-content/uploads/2018/04/Unbuffered.gif](https://hajsoftutorial.com/java/wp-content/uploads/2018/04/Unbuffered.gif)

아래는 그 반대의 경우인 버퍼를 사용하여 저장하는 경우를 보여주는 것이다.

[https://hajsoftutorial.com/java/wp-content/uploads/2018/04/bufferedoutput.gif](https://hajsoftutorial.com/java/wp-content/uploads/2018/04/bufferedoutput.gif)

아래는 버퍼 사용 유무 상관없이 텍스트를 파일에 쓸 수 있는 클래스들의 효율을 비교한 차트이다. 

당연히 1글자마다 디스크에 쓰기 작업을 수행하는 시스템 콜을 하는 FileOutputStream이 가장 느리다.

![image](https://user-images.githubusercontent.com/74949294/200461397-7787dc8e-b39c-41d9-b928-a06424a11ab8.png)

## FileWriter / BufferedWriter

FileWriter와 BufferedWriter는 모두 텍스트를 파일에 저장하기 위한 용도라 기능은 거의 같고 사용 가능한 메소드만 조금 다르다. 둘 다 버퍼를 사용하지만 효율면에서는 ****************************BufferedWriter****************************가 더 좋다. 

FileWriter에는 Buffered라는 단어가 안 붙어있지만 FIleWriter 또한 내부적으로 버퍼를 사용하고 있다. 

FileWriter는 OutputStreamWriter을 상속 받는데 write() 메소드도 여기서 상속받은 것이다. API 문서를 보면 OutputStreamWriter의 write() 메소드는 호출될 때 마다 주어진 문자열에 대해 인코딩 변환기가 호출된다고 하는데, 변환된 결과 바이트는 기본 출력 스트림에 쓰기 전 버퍼에 누적된다. 

**write() 메소드는 호출될 때마다 주어진 문자열에 대해 인코딩 변환기가 호출되는데 이를 피하고 싶은 경우 BufferedWriter 내에 래핑하는 것을 고려하라고 한다.**

![image](https://user-images.githubusercontent.com/74949294/200461418-f4a4fe13-f185-4123-9b30-e3dfda54da05.png)

## FileWriter / BufferedWriter 코드 상의 차이

************************BufferedWriter 객체는 FileWriter의 객체를 통해서만 만들 수 있다.************************

```java
FileWriter file = new FileWriter("test.txt");
file.write("test");
file.close();
```

```java
FileWriter file = new FileWriter("test.txt");
BufferedWriter bf = new BufferedWriter(file);
bf.write("test");
bf.close();
```

## BufferedWriter가 효율적인 상황

BufferedWriter 는 다음의 상황일 때 효율적이라고 한다.

1. 여러개의 write들이 있는 경우

2. 버퍼 사이즈에 비해 write들의 사이즈가 작은경우 -> 버퍼에 모아모아 다 차면 파일에 쓰는 호출이 그때서야 이뤄지므로

아래는 여러번의 small writes 를 하는 경우, BufferedWriter가 효율성을 증대시키는 경우이다.

버퍼의 크기가 다 채워지거나  flush() 되거나 close()를 만나거나 일 때만, 파일에 쓰여지는 호출이 이뤄진다.

```java
FileWriter file = new FileWriter("foo.txt");
BufferedWriter bf = new BufferedWriter(file);

for(int i = 0; i < 100; i++) {
    bf.write("foorbar");
    bf.write(NEW_LINE);
}
bf.close();
```
