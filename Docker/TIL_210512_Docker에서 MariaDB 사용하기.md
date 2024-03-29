# MariaDB연동(docker)

앞에서 만든 spring boot 프로젝트와 JPA 연동

Database는 Mariadb 사용하며, docker에 띄워서 사용



### Docker에 MariaDB 설치 및 실행

docker 이미지 다운로드

```
docker pull mariadb
```

*error 발생*

![img](https://lh5.googleusercontent.com/Tjbf63JC98Dek2J29r3hK0huYSmg8LIUpfQlG9i1o0Ck9tb6RSZhKla0PJPOt_kbECdZ7t4SWRqavtUdi5UIsITjPH6BbeMaCYDzPI-5kc72m_XRLqMkOPeoekUeHRFPSZebvBxq)

-> docker hub 로그인 문제



docker hub 로그인

```
docker login
```

![img](https://lh6.googleusercontent.com/LGYvnSEJl97vBHpRkXf12YJgWF_e2ewOeFZLIn_8w_RsM49IKAC8UZJHPm69w0Mh0U9ktamhsTpihIygLpjF7GiFXLDt8IhRjwRNzwtML22er5KmwhOKx1u2d-PtHK91FchsHFrj)



docker mariadb image 다운로드

``` 
docker pull mariadb
```

![img](https://lh6.googleusercontent.com/4MW0Pg4JjfoaGRKvtZV-i1DVmp5rB4DI3bpAxaJNgat3hr1z35w1YPVdUBOjzYsxOoqbJaEwQiIli8xW8PUFYGHZaA1LvCfB_WTa-SmaKPSvlUKvhCRehtEFoJ2RB_0yDBkrELuZ)



docker mariadb 컨테이너 추가 및 구동

```
docker run --name maraidb_noticeboard -d -p 4001:3306 -e MYSQL_ROOT_PASSWORD=yunha98 mariadb
```

-name : 컨테이너 이름 지정

-d : 컨테이너를 백그라운드로 실행

-p 4001:3306 : 호스트-컨테이너간 포트 연결. 호스트에서 4001로 접속 시 컨테이너 3306 포트로 포트포워딩

-e MYSQL~ : 컨테이너 내 환경변수 설정. root 계정 패스워드 설정

![img](https://lh3.googleusercontent.com/bcfB_HXWZR18EFd43WXkG2dzUU24fC3EL7qNkTqPp_dzsyyZSmzZqfb2Y21HUKLnmFrdeZZ9-QfCR37tPPHKpPlnNv51xIbEV8C18RiiAmQnm1LcvjoV4PCoyBmVHCnaRPFqgzMX)



컨테이너 목록 확인

```
docker ps
```

![img](https://lh6.googleusercontent.com/H3fCGptNW6JmEXD5GOjtRvSKxCbtWWTRsbr1Un5DEsdlowHKejC-SCG0UkJPbm1G5OuveYCxZqL8T7hzfqAwf7uVzL0GeAXsMjPkK-ImiE24XFCkeWuo4pBA2J6L5YNaHuCU1v6f)



컨테이너 bash로 접속

```
docker exec -it maraidb_noticeboard /bin/bash
```

![img](https://lh4.googleusercontent.com/hKmzpy_-mZkq-od4jZWFGO9_pBypehkm2buCxJ62blefshvJhZdK12LOYy5OoCnSOUP5NMByvAj12QAWpa_7CN1N5jpzQ-Dql2fF56DNl7R2Y4joeSjI157R-OD-joxIB5IvrabO)



### MariaDB 설정(user 및 데이터베이스 생성)

mariadb 실행

```
mysql -uroot -p'password'
```

![img](https://lh3.googleusercontent.com/qyzKoCW9VqA4mKwuRjaB_ly2Munbo-A0jKEWEfACfnmPdW-1PyrE8Q17zPs6zjQR15bNDMV9kMRmOD6T5COZqoYti2d57ZSiVSqr8w3W4sZSR0FNrm6XjWPlMgJ9wGuOPWcRa6Op)



user 생성 및 권한 부여

```sql
create user 'yunha'@'%' identified by 'yunha98';
grant all privileges on *.* to 'yunha'@'%';
flush privileges;
```

![img](https://lh5.googleusercontent.com/q9CYqP5lIEk83oaZH6zuh3yvSKcdHIhPYl8MzzZ7U4kpw9R1lYDkr19xgpWkW8xyMJbGgY4y_mLsr-b4w7ivkdc9ePsaM-Fe_kCR8afcIQO3N7Y_MeQqpHRwFfY2sncaRJNyfgck)



noticeboard 데이터베이스 생성

```sql
create database noticeboard;
```

![img](https://lh5.googleusercontent.com/kJKu4uQGfFsfNME8kkkIHJWvlhMtKG--CZE-qanBbIuFVPGma0eINqPQr6Z9n_YX8bGHSdtzWebWpI5rZyyjEHI5GA9CMS11lXhEe6X0ou5UfO2oDkAklFDgfJ7ed0NH7jUY09-D)



### Spring Boot Project와 MariaDB 연동

application.yml

- /src/main/resources/application.properties 파일을 application.yml로 바꾼다

```yaml
spring:
 datasource:
   url: jdbc:mariadb://localhost:4000/noticeboard
   username: yunha
   password: yunha98
   driver-class-name: org.mariadb.jdbc.Driver
```

![img](https://lh4.googleusercontent.com/yESnAIVw3yi6BCmsX6VH_goUI6HchCuueOmdzOnRe-x8hEkOlr0_xeDsv_UQ5tl8oCClSC9mJoOUwbpz9eqHlPyGnn3q45aL1VT-fDYXUJ1cFJlFQmpuxKknj8axcdJNVB8EA7dz)



build.gradle

- mariadb jdbc 의존성 추가

  ```
  implementation group: 'org.mariadb.jdbc', name:'mariadb-java-client'
  ```

  ![img](https://lh3.googleusercontent.com/LiMCnMuZWHF_1_uYEFr151fcdRMmKW_Y1oOOIlwoR5JAh8ViFLIdp74PxpPg0L8PBxV5odwLtqMlW-PrklzAXNutHvYUuzTcUDvEgFPWG5YUqF9UBqkWofi_6uUvWAjUQQgFhBEy)



### JPA 사용

application.yml

```yaml
jpa:
  hibernate:
    ddl-auto: update
    naming:
      physical-strategy: org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
    show-sql: true
    database-platform: org.hibernate.dialect.MariaDBDialect
```

ddl-auto : db 초기화 전략 설정

- update : 변경된 스키마만 적용
- create : 시작될 때 drop 후 다시 생성
- create-drop : 시작과 종료 시 모두 drop

physical-strategy : 변수면 매핑 전략

- ~.PhysicalNamingStrategyStandardImpl : 변수명과 DB 칼럼명 그대로 매핑
- ImprovedNamingStrategy : 카멜 케이스를 언더스코어로 변경(userName -> user_name)
