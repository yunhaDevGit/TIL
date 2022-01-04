## 주석의 종류

1) 구현 주석 (”/*  */”)

`개별적 구현`이나 `코드와는 상관 없는 주석`을 쓸 때 사용

2) 문서 주석 (”/**   */”)

`자바 클래스, 인터페이스, 생성자, 메서드, 필드` 등을 설명할 때 사용

문서 주석을 사용할 경우 Javadoc에 의해 HTML 형식의 문서를 생성

## 주석 규칙

> ***고려 사항***
*1. 가독성
2. 클래스의 “책임”을 3초 안에 파악할 수 있도록 짧고 간결하게 작성
3. Javadoc 태그를 적극 활용*
> 

1. return 값 반드시 설명
`@return` 태그 사용하여 리턴 값 명시

2. 예외 클래스 일 경우 어떤 경우에 던지는지 설명
+ 유명 스탠다드 예외들의 javadoc에서도 `Thrown(던진다)` 는 표현을 사용한다
    
    ```java
    /**
     * 할 일을 찾지 못했을 때 던집니다.
     */
    public class TaskNotFoundException extends RuntimeException {
    	...
    }
    ```
    
3. 구현에 의존하지 않을 것
구현 코드에 의존할 경우 code와 Javadoc 사이에 커플링이 생기므로 구현이 바뀌더라도 동일한 의미를 가진 주석을 작성
    
    ```java
    /**
     * <잘못된 주석>
     * 문자열의 앞부터 루프를 돌면서 문자열이 문자들의 시퀀스 s를 포함하는지 확인합니다.
     */
    public boolean contains(CharSequence s) {
    	...
    }
    ```
    
    ```java
    /**
     * <올바른 주석>
     * 문자열이 문자들의 시퀀스 s를 포함하는지 확인합니다.
     */
    public boolean contains(CharSequence s) {
    ```
    

### **태그 종류**

| @return 리턴 값 | 리턴 값에 대한 설명 |
| --- | --- |
| @author 이름 | 클래스나 인터페이스의 작성자 표시  |
| @version 테스트 | 클래스나 인터페이스에서의 버전 정보 |
| @param 매개변수 - 이름 설명  | 매개 변수에 대한 설명 |
| @exception or @throws | 메소드가 발생 시킬 수 있는 예외를 기술 |
| @see  | - 텍스트 및 외부 링크로의 사용
- 다른 필드나 메소드에 대한 참조 링크 표시 |
| @deprecated | 다음 버전에서 폐기된 메소드를 알림  |
| @serial | 기본적으로 직렬화 할 수 있는 클래스의 멤버를 설명  |
| @since | Tag를 가진 객체가 언제 추가되었는지 명시  |
| {@link #entity label} | 메소드나 필드의 상호 참조에 대한 링크를 표시 문서 텍스트 안에 링크가 생김 |
| {@doc-root} |  문서에 대한 루트 디렉토리에 대한 상대 경로 지정 |
| @hidden | API 문서 빌드 시 이 태그가 있을 경우 해당 항목은 숨김 |

## 주요 태그 사용법

### @return

- 리턴 값에 대해 설명
    - 메소드가 void를 리턴하거나 생성자가 아닌 경우를 제외하고 **모두 사용**
    - 명사형으로 짧게 작성
    - boolean 타입일 경우 `true/false` 의 경우를 명확히 작성
    
    ```java
    /**
     * @return int
     */
    public int getSize(){
    	return 0;
    }
    
    /**
     * @return 문자열이 s를 포함하고 있다면 true, 그렇지 않다면 false
     */
    public boolean contains(CharSequence s){
    	return true;
    }
    ```
    

### **@see**

- 텍스트
    
    ```java
    /**
     * 주석의 설명문
     * @see "string"
     */
    ```
    
- 외부 링크
    
    ```java
    /**
     * 주석의 설명문
     * @see <a href="http://www.naver.com">네이버</a>
     */
    ```
    
- 참조 링크
    
    ```java
    public class Size {
    	/**
    	 * 폭 반환
    	 * @return 폭
    	 * @see package.**class**#setSize(int, int)
    	 * @see package.Width#getWidth()
    	 */
    	public int getWidth() {
    		return 0;
    	}
    	/**
    	 * 높이 반환
    	 * @return 높이
    	 * @see package.**class**#setSize(int, int)
    	 * @see package.Width#getWidth()
    	 */
    	public int getWidth() {
    		return 0;
    	}
    }
    ```
    

### @throws

- 던지는 예외와 예외가 던져지는 상황에 대한 설명
    - 예외 타입 명시
    - 예외가 어떤 상황에 던져지는지 나열
- `@exception`과 똑같지만, `@throws`의 사용이 더 권장된다

```java
/**
 * @throws 예외클래스 설명
 */
```

### @param

- 파라미터의 의미에 대해 설명
    - 명사형으로 작성
    - 번역이 애매한 영어 단어는 그대로 작성
    
    ```java
    /**
     * @param 파라미터-이름 설명
     * @param <타입-파라미터-이름> 설명
     */
    ```
    

### References

[1] [https://johngrib.github.io/wiki/java-javadoc/#내가-javadoc-작성을-선호하는-이유](https://johngrib.github.io/wiki/java-javadoc/#%EB%82%B4%EA%B0%80-javadoc-%EC%9E%91%EC%84%B1%EC%9D%84-%EC%84%A0%ED%98%B8%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0)

[2] [https://danpatpang.github.io/tip/2018/04/12/Tip_java_comment/](https://danpatpang.github.io/tip/2018/04/12/Tip_java_comment/)
