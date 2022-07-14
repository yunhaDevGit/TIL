# Swagger Scan Package 지정

**pom.xml**
```xml
        <dependency>
            <groupId>org.springdoc</groupId>
            <artifactId>springdoc-openapi-ui</artifactId>
            <version>1.6.8</version>
        </dependency>
```

**application.yml**
```yaml
springdoc:
  packages-to-scan: {패키지경로}
```
