# hashCode()

- 객체의 해시코드(hash code)를 반환하는 메서드 (객체의 지문이라고도 불림)
- Object 클래스의 hashCode()는 객체의 주소를 int로 변환해서 반환
    
    ```java
    public class Object{
    	...
    	public native int hashCode(); // 내용X : OS의 메서드(C언어) 사용
    }
    ```
    
- equals()(=객체의 주소를 가지고 판단)를 오버라이딩하면, hashCode()도 오버라이딩 해야한다.
    - **equals() 결과가 true인 두 객체의 해시코드는 같아야 한다.**
        
        ```java
        // hashCode() 오버라이딩 : 이 부분 없으면 객체마다 해시코드는 다르게 나온다
        public int hashCode() {
        	return Objects.hash(kind, number);
        }
        ```
        
- System.identityHashCode(Object obj)는 Object 클래스의 hashCode()와 동일 (객체마다 다른 해시코드 반환)

## toString(), toString() 오버라이딩

- toString() : 객체를 문자열(String)으로 변환하기 위한 메서드
    - 대체로 오버라이딩을 통해 더 효율적으로 사용한다.
    
    ```java
    // 기본 toString()
    public String toString() {
    	return getClass().getName()+"@"+Integer.toHexString(hashCode()); // Test@dk2kse4s
    }
    
    // 오버라이딩 toString()
    public String toString() {
    	return "name:" + name + " age:" + age; // name: yunha age:25
    }
    ```
    
   
