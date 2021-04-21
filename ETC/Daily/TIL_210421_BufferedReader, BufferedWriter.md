# BufferedReader, BufferedWriter

- 버퍼를 이용해서 읽고 쓰는 함수

- 입출력

  - 키보드 -------------------- 프로그램

  - 키보드 ------버퍼-------- 프로그램

    바로 키보드로 입력하는 것보다 버퍼에 담아서 한번에 보내는게 더 빠르다

    -> 삽질을 해서 흙을 멀리 버릴때 한번씩 왔다갔다 하는 것보다, 수레에 담아 한번에 흙을 나르는게 더 효율적인 것과 같은 원리



#### BufferedReader

- **Scanner** 는 띄어쓰기와 엔터를 경계로 입력 값을 인식하여 따로 가공할 필요 x

- **BufferedReader** 는 엔터만 경계로 값을 인식하고 받은 데이터가 String으로 고정되어 가공하는 경우 많다. 속도는 **빠름**

  - 공백 단위로 데이터를 읽고 싶은 경우 **StringTokenizer의 nextToken 함수, String의 split 함수 이용**

  | Modifier and Type | Method and Description                                       |
  | :---------------: | ------------------------------------------------------------ |
  |       void        | close()                                                      |
  |       void        | mark(int, readAheadLimit)<br />스트림의 현재 위치를 마킹     |
  |      boolean      | markSupported()<br />스트림이 mark 기능을 지원하는지 true/false로 알려준다 |
  |      **int**      | **read()<br />한 글자만 읽어 정수형으로 반환<br />3을 '3'으로 읽어서 '3'의 정수형인 51 반환<br />3을 입력하여 3을 리턴해주는 함수가 아니다** |
  |      **int**      | **read(char[] cbuf, int offset, int length)<br />cbuf 의 offset 위치부터 length 길이만큼 문자를 스트림으로 읽어온다** |
  |    **String**     | **readLine()<br />한 줄을 읽어 String으로 반환**             |
  |      boolean      | ready()<br />입력 스트림이 사용할 준비가 되었는지 확인. 1이면 준비 완료 |
  |       void        | reset()<br />마킹이 있으면 그 위치부터 다시 시작, 그렇지 않으면 처음부터 다시 시작 |
  |       long        | skip(long n)<br />n개의 문자를 건너뛴다                      |

  ```java
  BufferedReader bf = new BufferedReader(new InputStreamReader(System.in));
  String s = bf.readLine();
  int i = Integer.parseInt(bf.readLine());
  
  // ----데이터 가공----
  
  StringTokenizer st = new StringTokenizer(s);
  int a = Integer.parseInt(st.nextToken());
  
  String array[] = s.split(" ");
  ```



#### BufferedWriter

- System.out.println(""); 와 동일하게 사용 가능한 함수
- 성능면에서 좋다 -> 많은 출력을 할 때 사용하면 좋다

```java
BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
String s = "abcdefg";
bw.write(s); // 출력
bw.newLine(); // 줄바꿈
bw.flush(); // 남아있는 데이터를 모두 출력
bw.close(); // 스트림 닫기
```

