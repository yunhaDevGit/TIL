그동안 웹 개발을 하면서 URL이라는 용어를 그저 자연스럽게(?) 사용하였다.

그러나 정작 URL이 무슨 약자인지, URI는 뭔지 또 URI와 URL의 차이점은 뭔지에 대한 것은 정확히 알지 못한 채 사용했던 것 같다. 그래서 이번 기회에 각각에 대한 차이를 명확히 이해하고 정리하려고 한다.

### **URI(Uniform Resource Identifier)**

URI는 웹 서버가 리소스를 고유하게 식별할 수 있도록 하는 **통합 자원 식별자**이다.

쉽게 말해, 인터넷에 있는 자료의 ID라고 생각하면 이해하기 쉬울 거다.

예를 들어, 어떠한 자료가 있는데 이 자료의 이름이 다른 자료와 이름이 같다면 찾을 수 없다. 그러므로 해당 자료의 이름은 유일해야 한다. 즉, **URI는 유일해야 한다. **

#### **URI는 URL과 URN 두가지가 있다.**

<img src="https://blog.kakaocdn.net/dn/zbafq/btq4rzyE2Gn/6j1CvDXUvKA0823LrcBld0/img.png" alt="img" style="zoom:80%;" />

-   **URL(Uniform Resource Locator)**
    
    **자원의 위치** 특정 서버의 한 리소스에 대해 구체적인 위치를 서술한다.  
    
    특정한 정보 및 자료에 접근하는 데 있어서 네트워크 상의 주소를 말한다.  
    
    **URL 표현 방법**
    
    **scheme://host:port/url-path**

```
ex) http://test.com
    http://test.com/static/test.png
    http://test.com/main?name=yunha&age=24
```

-   **URN(Uniform Resource Name)** 
    
    **자원의 이름** 리소스가 어디에 있든 찾을 수 있는 방식이다.   
    
    URL은 도메인과 파일 경로에 따라 네트워크 주소에 영향을 받지만, URN은 특정 정보에 이름을 부여해서 찾기 때문에 도메인 및 파일 경로에 영향을 받지 않는다.  
    
    즉, 세부적인 정보의 위치가 바뀌어도 동일하게 접근할 수 있다.  
    
    **URN 표현 방법**  
    
    **urn(URN):NID(Namespace):NSS(Namespace Specific String)**

```
ex) urn:ietf:rfc:2141

```

### **URI와 URL 비교**

URI와 URL 개념을 학습하면서 가장 헷갈리고 어려웠던 부분이 어디까지가 URI이고, 어디까지가 URL인지 구분하는 것이었다. URI와 URL의 차이에 대해 많은 사람들이 그저 "URI가 더 상위 개념이고, URL은 하위 개념이다. " 정도로 알고 있는 경우가 많았고, 차이점을 서술한 많은 블로그들의 내용이 상이했기 때문에 더더욱 헷갈렸던 것 같다. 

솔직히 말하면 **"URI가 더 상위 개념이고, URL은 하위 개념이다. "** 이 정도만 알아도 URI와 URL이란 용어를 적당히 구분해가면서 사용할 수 있을 거라고 생각한다. ~그냥 URI라고 하면 되니까....ㅎ~

하지만 난 URI와 URL에 대해 학습하고 차이점을 명확히 알기 위해 학습을 시작했으니 더 찾아보기로 했다. 

처음에는 대다수의 글에서 URL은 https://somewhere/search?q=uri 라는 주소가 있을 때, https://somewhere/search 까지가 URL이고, 내가 원하는 정보를 얻기 위해서는 q=url이라는 식별자가 필요하므로 https://somewhere/search?q=uri은 URI지만 URL은 아니라고 하였다. 

이러한 글이 지배적이었기 때문에 나또한 그런가 보다 생각하고 정리를 하던 중  [sowells.tistory.com/193](https://sowells.tistory.com/193) 해당 글을 읽고 URL과 URI의 차이점에 대해 다시 찾아보게 되었다. 

결론만 말하자면 우선 **URL은 쿼리스트링까지 포함**한다.



![img](https://blog.kakaocdn.net/dn/ciLyVF/btq4spJng9g/NAGcv6d1LNRbWXnmF1c4sk/img.png)

schem - 사용할 프로토콜

host - 호스트명 또는 IP주소

port - 항상 필요하지만 생략 가능(schem에 따라 기본 포트 적용 ex-http:80, https:443)

query - 질의 문자열

fragment - 유일하게 서버로 전달하지 않는 값. 브라우저 내부에서 사용하는 문맥

RFC 1738 문서에 따르면 URI와 URL의 표현 방법은 각각 다음과 같이 정의되어 있다.

**URL**

<table style="border-collapse: collapse; width: 100%;" border="1"><tbody><tr><td style="width: 100%;">schem://&lt;user&gt;:&lt;password&gt;@&lt;host&gt;:&lt;port&gt;/&lt;url-path&gt;<br><br>가장 많이 사용되는 HTTP URL의 표현방법<br>http://&lt;host&gt;:&lt;port&gt;/&lt;path&gt;?&lt;searchpart&gt;</td></tr></tbody></table>

**URI**

<table style="border-collapse: collapse; width: 100%; height: 17px;" border="1"><tbody><tr style="height: 17px;"><td style="width: 100%; height: 17px;">schem:[//[user[:password]@host[:port]][/path][?query][#fragment]</td></tr></tbody></table>

_**즉, PATH와 쿼리스트링 모두 URL에 포함된다.**_

_또한 URI는 URL과 URN을 포함하고 있는 상위 개념이다. URL은 URI이지만 URI가 항상 URL은 아니다._

URI와 URL에 대해서는 [RFC 1738](https://tools.ietf.org/html/rfc1738#section-3.1) 문서에 자세히 기술되어 있으니 더 궁금한 점이 있다면 참고하길 바란다.

참고

[ko.wikipedia.org/wiki/URL](https://ko.wikipedia.org/wiki/URL)

[https://sowells.tistory.com/193](https://sowells.tistory.com/193)

[https://futurists.tistory.com/95](https://futurists.tistory.com/95)

https://velog.io/@jch9537/URI-URL
