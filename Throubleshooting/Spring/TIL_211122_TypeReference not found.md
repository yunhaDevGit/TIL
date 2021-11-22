# class file for com.fasterxml.jackson.core.type.TypeReference not found

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/8e1e586d-0fcd-44ee-989e-d5f672c8e889/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211122%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211122T084549Z&X-Amz-Expires=86400&X-Amz-Signature=c02d918eda152349b1c109c688a6cd7697dd970f1051bea72ec528e70262cccd&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

```java
Task :kafka-common:compileJava FAILED
/root/IdeaProjects/spring-kafka/cloudit6-kafka/kafka-common/src/main/java/io/kafka/cloud/kafkacommon/config/VmKafkaConsumerConfig.java:27: error: cannot access TypeReference
        new ErrorHandlingDeserializer(new JsonDeserializer<>(VmDto.class)));
                                      ^
  class file for com.fasterxml.jackson.core.type.TypeReference not found
Note: Some input files use unchecked or unsafe operations.
Note: Recompile with -Xlint:unchecked for details.
1 error
```

build.gradle

```
gradle.projectsEvaluated {
    tasks.withType(JavaCompile){
        options.compilerArgs << "-Xlint:unchecked"
    }
}
```

위의 설정도 해주었으나 여전히 같은 문제가 나옴...

build.gradle

```
implementation 'com.fasterxml.jackson.core:jackson-core:2.12.5'
implementation 'com.fasterxml.jackson.core:jackson-databind:2.12.5'
```

위의 dependency를 추가해주니까 된다....
