# StringTokenizer

- StringTokenizer 클래스는 문자열을 우리가 지정한 구분자로 쪼개주는 클래스
	- 쪼개진 문자열을 토큰(Token)이라고 부른다
- java.util.StringTokenizer
- 메소드
	- **생성자**
		- **public StringTokenizer(String str)** : 전달된 매개변수 str을 기본(default) delim으로 분리. 기본 delimiter는 공백
		- **public StringTokenizer(String str, String delim)** : 특정 delim으로 문자열 분리 (구분자는 토큰으로 간주 x)
		- **public StringTokeizer(String str, String delim, boolean returnDelims)** : str을 특정 delim으로 분리시키는데 그 delim까지 token으로 포함할지를 결정. true - 포함, false - 포함 x, default - false ( 구분자를 토큰으로 간주 여부 설정 가능)
	- **int countTokens()**
		- 남아있는 token의 개수를 반환. 전체 token의 갯수가 아닌 *현재 남아있는 token 개수*
	- **boolean hasMoreElements(), boolean hasMoreTokens()**
		- 다음 token 반환. StringTokenizer는 내부적으로 어떤 위치의 토큰을 사용하였는지 기억하고 있고 그 위치를 다음으로 옮긴다. 
	- **Object nextElements(), String nextToken()**
		- 다음 token을 반환. 두 메서드는 반환형이 다르다.

StringTokenizer는 단 한 개의 구분자만 사용할 수 있기 때문에 복잡한 형태의 구분자로 문자열을 나눠야 하는 경우, Scanner나 split을 사용해야 한다.


#### 띄어쓰기를 기준으로 문자열 분리
```java
import java.util.StringTokenizer;
public class Main{
	public static void main(String[] args){
		String str = "String Tokenizer 사용법 알아보기";
		StringTokenizer st = new StringTokenizer(str);

		System.out.println(st.nextToken()); // String
		System.out.println(st.nextToken()); // Tokenizer
		System.out.println(st.nextToken()); // 사용법
		System.out.println(st.nextToken()); // 알아보기
```

#### 구분자를 기준으로 문자열 분리
```java
import java.util.StringTokenizer;
public class Main{
	public static void main(String[] args){
		String str = "화!이!팅";
		StringTokenizer st = new StringTokenizer(str,!);

		while(st.hasMoreTokens()){
			System.out.println(st.nextToken());
		}
```
```
화
이
팅
```

#### 구분자를 기준으로 문자열 분리(구분자 포함)
```java
import java.util.StringTokenizer;
public class Main{
	public static void main(String[] args){
		String str = "화!이!팅";
		StringTokenizer st = new StringTokenizer(str,!,true);

		while(st.hasMoreTokens()){
			System.out.println(st.nextToken());
		}
```
```
화
!
이
!
팅
```

### BufferedReader와 함께 사용
```java
import java.util.StringTokenizer;
public class Main{
	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in)); // 6 3
		StringTokenizer st = new StringTokenizer(br.readLine());
		
		int n = Integer.parseInt(st.nextToken()); // 6
		int m = Integer.parseInt(st.nextToken()); // 3
	}
}
```
