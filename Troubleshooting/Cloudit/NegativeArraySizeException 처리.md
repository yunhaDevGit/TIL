## **java.lang.NegativeArraySizeException**  처리

```java
public static CMD_RESULT deleteFileComplete(String filePath) throws IOException {

	OutputStream outputStream = null;
	try {
		File file = new File(filePath);
		long length = file.length();
		logger.info("file length = ", length);
		outputStream = new FileOutputStream(filePath);
		SecureRandom secureRandom = new SecureRandom();
		byte[] bytes = new byte[(int) length];
		secureRandom.nextBytes(bytes);

		outputStream.write(bytes);
	} catch (IOException e) {
		logger.info("exception = ", e.getMessage());
		e.printStackTrace();
	}

	return CMD_RESULT.SUCCESS;
}
```

File 바이트 크기를 읽어 온 후, 해당 바이트 크기만큼 랜덤 난수를 생성하여 덮어씌운다.



이때, **java.lang.NegativeArraySizeException** 발생

-> long 타입의 length를 int로 변경하여 byte 배열 선언하는 과정에서 위의 예외 발생



**해결** :smile:  --- 해결 아니었음 :cry:

```java
...
SecureRandom secureRandom = new SecureRandom();
byte[] bytes = Longs.toByteArray(length);
secureRandom.nextBytes(bytes);
...
```



----

해결 된 줄 알았는데 아니었다ㅠㅠ

Longs.toByteArray(length) 로 하면 파일 크기가 왜인지 모르겠지만 8로 할당 된다....

그래서 두번 째 방법인 ByteBuffer buffer = ByteBuffer.allocate() ~ 이런식으로 했는데 결과가 똑같았다.

```java
...
SecureRandom secureRandom = new SecureRandom();
ByteBuffer buffer = ByteBuffer.allocate(8);
byte[] bytes = buffer.array();
secureRandom.nextBytes();
...
```

ByteBuffer.allocate() -> ByteBuffer 크기 할당

위와 같은 결과가 나왔다.



-----

**진짜 해결** :smile:

java에서 배열에 2GB 이상의 크기를 할당할 수 없다.

또한 int 데이터형이 받을 수 있는 숫자를 넘어선 값을 int로 캐스팅하려고 해서 exception이 발생했다.

만약 배열에 할당할 수 있는 크기를 넘어가면 그 길이를 배열 할당 최댓값보다 작을 때까지 2로 나눈 후, 순환문을 돌며 처리를 해준다.

```java
public static CMD_RESULT deleteFileComplete(String filePath) throws IOException {

	OutputStream outputStream = null;
    try {
        File file = new File(filePath);
        long length = file.length();
        logger.debug("file length = {}", length);
        outputStream = new FileOutputStream(filePath);
        SecureRandom secureRandom = new SecureRandom();
        byte[] bytes;
        int count = 1;

        if(length >= Integer.MAX_VALUE){

        	while(length >= Integer.MAX_VALUE){
        		length /= 2;
        		count *= 2;
        	}

	        bytes = new byte[(int) length];

    	    for (int i = 0; i < count; i++) {
        		secureRandom.nextBytes(bytes);
        		outputStream.write(bytes);
        	}

        } else {
        	bytes = new byte[(int)length];
        	secureRandom.nextBytes(bytes);
        	outputStream.write(bytes);
      	}
        
    } catch (IOException e) {
      logger.error("exception = ", e.getMessage());
      e.printStackTrace();
    }

	return CMD_RESULT.SUCCESS;
}
```



