

### filePath 경로에 있는 파일들의 byte 수를 읽어와서 해당 byte 수만큼의 난수를 생성하여 덮어 씌우기

*vm, template, snapshot에 있는 정보를 각각의 가상 자원을 삭제할 때 알수 없는 값으로 변경하여 삭제 하기 위함*

-> 가상 자원을 삭제하였는데 누군가 복구 했을 때, 노출되면 안되는 정보들이 노출 될 위험이 있기 때문에 아무런 상관 없는(?) 문자로 덮어 쓴 후, 삭제하여 복구 불가능하게끔 한다.



:exclamation: 문제 상황 :exclamation:

각 파일의 바이트 수를 읽어 해당 바이트 수만큼 랜덤 값이 써져야 하는데, 파일에 랜덤 값이 써지지 않고 안의 내용만 지워진다

```java
public static CMD_RESULT deleteFileComplete(String filePath) throws IOException {

	OutputStream outputStream = null;
	try {
		outputStream = new FileOutputStream(filePath);
		File file = new File(filePath);
		long length = file.length();

		SecureRandom secureRandom = new SecureRandom();
		byte[] bytes = new byte[(int) length];
		secureRandom.nextBytes(bytes);

		outputStream.write(bytes);
	} catch (IOException e) {
		e.printStackTrace();
	}

	return CMD_RESULT.SUCCESS;
}

public static CMD_RESULT deleteFolderComplete(String filePath) throws IOException {
	Collection<File> fileCollection = FileUtils
        .listFiles(new File(filePath), TrueFileFilter.INSTANCE, TrueFileFilter.INSTANCE);
	for (File file : fileCollection) {
		deleteFileComplete(Paths.get(filePath, file.getName()).toString());
	}

	return CMD_RESULT.SUCCESS;
}
```





#### **:smile: 해결!!**

**원인 : FileOutputStream은 데이터를 파일에 바이트 스트림으로 저장하기 위해 사용되는 클래스이다.**

FileOutputStream(File file) 생성자는 주어진 File 객체가 가리키는 파일을 쓰기 위한 객체를 생성하는데, 만약 **기존의 파일이 존재할 경우, 그 내용을 지우고 새로운 파일을 생성**하기 때문에 위처럼 작성할 경우, **새로운 파일을 생성** 후 해당 파일의 바이트 수를 읽기 때문에 0으로 읽고, 아무런 값이 써지지 않았다. 

우선 원하는 파일의 길이를 읽은 후, 그 다음 fileOutPutStream(File file) 생성자를 통해 새로운 파일을 생성 후 값을 적는 방식으로 변경

```java
public static CMD_RESULT deleteFileComplete(String filePath) throws IOException {

	OutputStream outputStream = null;
	try {
		File file = new File(filePath);
		long length = file.length();
		outputStream = new FileOutputStream(filePath);

		SecureRandom secureRandom = new SecureRandom();
		byte[] bytes = new byte[(int) length];
		secureRandom.nextBytes(bytes);

		outputStream.write(bytes);
	} catch (IOException e) {
		e.printStackTrace();
	}

	return CMD_RESULT.SUCCESS;
}

public static CMD_RESULT deleteFolderComplete(String filePath) throws IOException {
	Collection<File> fileCollection = FileUtils
        .listFiles(new File(filePath), TrueFileFilter.INSTANCE, TrueFileFilter.INSTANCE);
	for (File file : fileCollection) {
		deleteFileComplete(Paths.get(filePath, file.getName()).toString());
	}

	return CMD_RESULT.SUCCESS;
}
```







-------

FileOutputStream, FileInputStream, OuputStream, InputStream,,, 좀 더 찾아보기