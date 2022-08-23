# class file for com.fasterxml.jackson.core.type.TypeReference not found

![image](https://user-images.githubusercontent.com/74949294/186103899-1c91691e-7a70-4592-a252-2c82cdfc7742.png)

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
