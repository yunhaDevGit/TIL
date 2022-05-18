# Path Variable과 Query Parameter 각각 사용 경우

### Query Parameter

`Query Parameter`는 경로 뒤에 입력 데이터를 함께 제공하는 식으로 사용한다.

```java
/post?post_id=5
```

`?` 이후의 부분을 query string이라고 하며 뒤는 key, value 쌍으로 이루어진다. 

아래와 같이 &로 여러 개의 데이터를 넘길 수도 있다.

```java
/post?post_id=5&key1=value1
```

### Path Variable

`Path Variable`은 경로를 변수로서 사용한다. 

여러 개의 게시물이 있을 때 각각의 게시물을 조회할 경우 게시물의 id가 필요하다. 

이때 게시물의 id를 path로 넘겨주는 방법이다.

```java
/post/6  // /post/{id}
```

### Path Variable VS Query Parameter

각각 어떨 때 사용하는 게 좋을까?

서버는 어느 방식으로 넘어오던지 resource의 id로 쿼리를 날린다. 

만약에 존재하지 않는 resource id가 넘어올 경우 각각 다음과 같이 동작하게 된다. 

`Path Variable`의 경우 해당 경로의 페이지가 존재하지 않기 때문에 404 에러를 발생한다. 

반면 `Query Parameter`는 서버로 데이터가 넘어가고 쿼리를 날리며 해당하는 데이터가 없을 경우 따로 에러 핸들링을 해줘야 합니다. 

즉, **resource를 식별해야 하는 경우 `Path Variable`이 더 적합**하다. 

반대로 **정렬이나 필터링**을 한다면 **Query Parameter**를 사용하는 것이 더 적합하다.

[https://ryan-han.com/post/translated/pathvariable_queryparam/](https://ryan-han.com/post/translated/pathvariable_queryparam/)

[https://ssungkang.tistory.com/entry/Web-Path-Variable-VS-Query-Parameter](https://ssungkang.tistory.com/entry/Web-Path-Variable-VS-Query-Parameter)
