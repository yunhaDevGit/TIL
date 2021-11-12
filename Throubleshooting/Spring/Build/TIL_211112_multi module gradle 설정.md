# Spring Boot multi-module gradle 설정

Spring Kafka 예제를 만들기 위한 멀티 모듈 구성을 하는 과정에서 build.gradle 설정으로 반나절 가까이 애를 먹었습니다.
gradle 빌드 툴을 사용하여 멀티 모듈 구성을 처음 하게 되었는데 정말 너무 힘들었다....ㅠ

*아래 과정은 제가 설정한 순서대로 작성되었고, 해결 방법 및 정상적인 설정 코드는 가장 아래에서 확인할 수 있습니다.*

총 세가지 모듈을 만들었고 각 모듈은 다음과 같습니다.
- kafka-common : 아래 두 모듈이 공통으로 사용하는 모듈 (parent module)
- kafka-producer 
- kafka-consumer

우선 가장 먼저 Root Project를 만들었습니다.
![](https://lh4.googleusercontent.com/qcjR1l3HbruyMzccapx8OHSowdqnFL-2dpyhIBtB37m7YSBQrMTB3zeqCiGilrhZZY1FcY_z4ZcBU5u27RUySIvsnmWnuJso7AT8jhqmnzeH03jMhFsbCGFzEHofW6TFeWseLG_R)

Root Project를 만든 후 Root Project에서 우클릭 > New > Module을 통해 kafka-common 모듈을 생성했습니다.
한 번에 모든 모듈을 만들지 않고, 모듈 하나씩 추가하는 과정으로 진행했고, 아래와 같이 설정해주었습니다.



### Root 프로젝트 설정

먼저 아래와 같이 루트 프로젝트의 build.gradle을 설정해주었습니다.

```
plugins {
    id 'org.springframework.boot' version '2.5.6'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    id 'java'
}

group = 'io.spring.kafka'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '1.8'

repositories {
    mavenCentral()
}

dependencies {
    implementation "org.springframework.boot:spring-boot-gradle-plugin"
    implementation "io.spring.gradle:dependency-management-plugin"
}

subprojects {
    apply plugin: 'java'
    apply plugin: 'org.springframework.boot'
    apply plugin: 'io.spring.dependency-management'

    group = 'io.spring.kafka'
    version = '0.0.1-SNAPSHOT'
    sourceCompatibility = '1.8'

    repositories {
        mavenCentral()
    }

    dependencies {
        implementation 'org.springframework.boot:spring-boot-starter'
        compileOnly 'org.projectlombok:lombok'
        annotationProcessor 'org.projectlombok:lombok'
        testImplementation 'org.springframework.boot:spring-boot-starter-test'
    }
}

test {
    useJUnitPlatform()
}

project(':kafka-producer') {
    dependencies {
        implementation project(':kafka-common')
    }
}

project(':kafka-consumer') {
    dependencies {
        implementation project(':kafka-common')
    }
}
```

하위 모듈인 kafka-common을 참조 및 관리 할 수 있도록 하기 위해 아래와 같이 루트 프로젝트의 settings.gradle에 kafka-common을 include 해주었습니다.

```
rootProject.name = 'cloudit6-kafka'  
  
include 'kafka-common'
```

이렇게 루트 프로젝트의 설정을 마치고 kafka-common 모듈의 build.gradle 설정을 해주었습니다. 

###  하위 모듈 설정

build.gradle을 다음과 같이 설정해준 뒤 build를 했습니다.

```
plugins {  
  id 'org.springframework.boot' version '2.5.6'  
  id 'io.spring.dependency-management' version '1.0.11.RELEASE'  
  id 'java'  
}  
  
group = 'io.kafka.cloud'  
version = '0.0.1-SNAPSHOT'  
sourceCompatibility = '1.8'  
  
repositories {  
  mavenCentral()  
}  
  
dependencies {  
  implementation group: 'org.springframework.boot', name: 'spring-boot-starter'  
  testImplementation 'org.springframework.boot:spring-boot-starter-test'  
}  
  
test {  
  useJUnitPlatform()  
}
```

해당 설정을 한 후 build 실행을 했는데 루트 프로젝트가 정상적으로 build 되지 않았습니다.
결국 build.gradle 작성 방법(?)에 대해 다시 찾아보았고 루트 프로젝트의 build.gradle을 아래와 같이 수정해주었습니다.

```
buildscript {  
  ext {  
  springBootVersion = '2.5.6'  
  }  
  repositories {  
  mavenCentral()  
    }  
  dependencies {  
  classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")  
        classpath "io.spring.gradle:dependency-management-plugin:1.0.11.RELEASE"  
  }  
}  
  
allprojects {  
  group = 'io.kafka.cloud'  
  version = '0.0.1-SNAPSHOT'  
}  
  
subprojects {  
  apply plugin: 'java'  
  apply plugin: 'org.springframework.boot'  
  apply plugin: 'io.spring.dependency-management'  
  
  sourceCompatibility = 1.8  
  
  repositories {  
  mavenCentral()  
    }  
  
  dependencies {  
  testImplementation group: 'junit', name: 'junit', version: '4.12'  
  implementation 'org.springframework.boot:spring-boot-starter'  
  testImplementation 'org.springframework.boot:spring-boot-starter-test'  
  }  
}  
  
project(':kafka-common') {  
}  
```

위와 같이 설정 변경 후 루트 프로젝트는 정상적으로 빌드가 되었습니다. 
그 후 kafka-common을 build 해주었는데 아래와 같은 에러가 발생하였습니다.

![](https://lh3.googleusercontent.com/kuLLVVhhRkIUPtvWpUb5c2SVm2YRySJqKcu4xIxNAITJa-yrwjfR7yHXiMPFWhRqCo-URN8imu4JIvtjMSBYZDNKnciyLjshrx81nflsx2H4x5XvqpnAdDqzI_GB-Ydb7jdxydHG)
```
Error resolving plugin [id: 'org.springframework.boot', version: '2.5.6']
> Plugin request for plugin already on the classpath must not include a version
```
이미 부모 프로젝트에 명시가 되어 있어 해당 버전 정보를 지우라는 에러 같아 버전 정보를 지워보고 다른 버전으로도 해보았지만 여전히 동일한 에러가 발생했습니다 .
부모인 루트 프로젝트의 build.gradle을 확인해보니 subproject 항목에 동일한 설정 정보가 있어 충돌 나서 발생한 문제였습니다.

아래와 같이 kafka-common 모듈의 build.gradle에서 plugins 및 루트 프로젝트에 설정 되어 있는 정보들을 모두 주석해주었더니 정상적으로 build가 되었습니다.

```
//plugins {  
//    id 'org.springframework.boot' version '2.5.6'  
//    id 'io.spring.dependency-management' version '1.0.11.RELEASE'  
//    id 'java'  
//}  
  
//group = 'io.kafka.cloud'//version = '0.0.1-SNAPSHOT'//sourceCompatibility = '1.8'  
//repositories {  
//    mavenCentral()  
//}  
  
dependencies {  
}  
  
test {  
  useJUnitPlatform()  
}
```

동일한 방법으로 kafka-producer와 kafka-consumer 모듈 생성 후 build.gradle을 동일하게 설정해주었습니다.
추가로 루트 프로젝트의 settings.gradle과 build.gradle에 아래 설정을 추가해주었습니다.
**settings.gradle**
```
...  
include 'kafka-producer'  
include 'kafka-consumer'
```

**build.gradle**
```
...
project(':kafka-producer') {  
  dependencies {  
  implementation project(':kafka-common')  
    }  
}  
  
project(':kafka-consumer') {  
  dependencies {  
  implementation project(':kafka-common')  
    }  
}
```

그러나 build를 실행했을 때 또다시 에러가 발생했습니다. 

![](https://lh3.googleusercontent.com/6vC4hkd5ykXkKoXM-KnBAnWMI0Efi3INdznOMs1qDacttHyqo4qHqohwJZG3jvA3guD68APNzQvESuyKuWM6-peZoruCxMt4FsoOZ9Zkp1ntCLQFsX0Mc4uxbr8jB3j7jO8WFufc)

한참을 헤매던 중 
https://stackoverflow.com/questions/45387971/when-to-use-gradle-properties-vs-settings-gradle/47825535
해당 글을 보고 settings.gradle 파일이 루트 프로젝트에만 두고 다른 하위 모듈에서 지워줘야 한다는 것을 확인하여 하위 모듈들의 settings.gradle을 제거해주니 정상적으로 build가 되었습니다.

![](https://lh4.googleusercontent.com/q1AQinv8cRgGi6X9L9kmP97ypLnYM3NijglwV_McV7wsIC3ZZawklxq-bZXmf9biKm0NrVnoaYJFpU9ngFUoVbKiQ5QS_K872R3SZSKTWkl_iP_U-NRfaVXIGr9A6nPy3POZB_VK)


최종적으로 제가 작성한 설정 정보와 디렉토리 구조는 다음과 같습니다.

![](https://lh6.googleusercontent.com/aV43h7LzX7L7wr-XIYyWaWK6Jobsh8r-KQoX2O1IXg4lbeO1vJOGgh-nU-SNBcI1DrLGWAqbMyVHNPNkQxZT7rnETNS1S23qyBCE_O9CDS0zvq_yIIXga2VG8AGQodrp9NK5JKqJ)

**Root Project (settings.gradle)**
```
rootProject.name = 'cloudit6-kafka'  
  
include 'kafka-common'  
include 'kafka-producer'  
include 'kafka-consumer'
```

**Root Project (build.gradle)**
```
buildscript {  
  ext {  
  springBootVersion = '2.5.6'  
  }  
  repositories {  
  mavenCentral()  
    }  
  dependencies {  
  classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")  
        classpath "io.spring.gradle:dependency-management-plugin:1.0.11.RELEASE"  
  }  
}  
  
allprojects {  
  group = 'io.kafka.cloud'  
  version = '0.0.1-SNAPSHOT'  
}  
  
subprojects {  
  apply plugin: 'java'  
  apply plugin: 'org.springframework.boot'  
  apply plugin: 'io.spring.dependency-management'  
  
  sourceCompatibility = 1.8  
  
  repositories {  
  mavenCentral()  
    }  
  
  dependencies {  
  testImplementation group: 'junit', name: 'junit', version: '4.12'  
  implementation 'org.springframework.boot:spring-boot-starter'  
  testImplementation 'org.springframework.boot:spring-boot-starter-test'  
  }  
}  
  
project(':kafka-producer') {  
  dependencies {  
  implementation project(':kafka-common')  
    }  
}  
  
project(':kafka-consumer') {  
  dependencies {  
  implementation project(':kafka-common')  
    }  
}
```

**Sub Module (build.gradle)**
```
dependencies {  
	// 개별 모듈이 추가하고 싶은 의존 정보 작성
}  
  
test {  
  useJUnitPlatform()  
}
```
