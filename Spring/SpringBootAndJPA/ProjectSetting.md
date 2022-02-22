# 프로젝트 환경설정

_김영한 강사님의 실전! 스프링 부트와 JPA 활용1 - 웹 애플리케이션 개발 강의를 정리한 내용입니다._

### JPA와 DB 설정

```java
// 경로 : main/resources/application.yml
// yml은 띄어쓰기 2칸으로 계층을 만들기 때문에 조심하기!
spring:
  datasource:
    url: jdbc:h2:tcp://localhost/~/jpashop
    username: sa
    password:
    driver-class-name: org.h2.Driver
  jpa:
    hibernate:
      ddl-auto: create
    properties:
      hibernate:
# show_sql: true
       format_sql: true
logging.level:
  org.hibernate.SQL: debug
# org.hibernate.type: trace
```
- <span style='background-color: #fff5b1'>spring.jpa.hibernate.ddl-auto: create</span> : 애플리케이션 실행 시점에 테이블을 drop 하고, 다시 생성한다.
🚨 **운영 단계에서 절대 사용하지 말기** ~~데이터 다 날아감~~ 🚨
- show_sql : System.out에 하이버네이트 실행 SQL을 남긴다.
- org.hibernate.SQL : logger를 통해 하이버네이트 실행 SQL을 남긴다.

#### 쿼리 파라미터 로그 남기기
- 로그에 다음을 추가하기 org.hibernate.type(SQL 실행 파라미터를 로그로 남김)
- 스프링부트 사용시 아래 라이브러리 추가
```java
implementation 'com.github.gavlyukovskiy:p6spy-spring-boot-starter:1.5.6'

```
