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



**해결** :smile:

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

