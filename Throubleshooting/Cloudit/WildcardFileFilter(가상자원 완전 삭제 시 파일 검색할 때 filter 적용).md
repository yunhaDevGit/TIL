### 가상자원 완전 삭제 시 파일 검색할 때 filter 적용

```centos
example
$ cd /etc/hello
$ ls
hello
hello.conf
hello.text
hi
```

다음과 같이 /etc/hello 디렉토리에 hello, hello.conf, hello.text, hi 파일이 있다고 하자.

그때 hello, hello.conf, hello.text 파일을 완전 삭제하고자 했다.

*완전 삭제 : 각 파일의 바이트 수를 읽어와서 그 바이트 수만큼의 난수를 생성하여 넣은 후 삭제*

같은 디렉토리 안에 있는 모든 파일이 아닌 해당 같은 이름의 다른 확장자를 가진 파일에 대해 모두 완전 삭제하기 위해, IOFileFilter의 WildcardFileFilter를 통해 필터 적용하여 파일 검색을 하였다. 

```java
public static CMD_RESULT deleteTemplateComplete(String filePath, String templateId) throws IOException {
	IOFileFilter fileFilter = new WildcardFileFilter(templateId+"*");
	Collection<File> fileCollection = FileUtils
        .listFiles(new File(filePath), fileFilter, TrueFileFilter.INSTANCE);
	for (File file : fileCollection) {
		deleteFileComplete(Paths.get(filePath, file.getName()).toString());
	}

	return CMD_RESULT.SUCCESS;
}
```

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



----



#### WildcardFileFilter

특정 이름, 확장자의 파일을 찾고 싶을 때 사용

```java
// example

String filePath = "/var/hello";

File file = new File(filePath);
FileFilter fileFilter = new WildcardFileFilter(id + "*.txt");
File[] files = file.listFiles(fileFilter);

for(int i=0;i<files.length;i++)
    System.out.println(files[i]);
```









