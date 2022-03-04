_스프링부트와 AWS로 혼자 구현하는 웹서비스 책을 정리한 내용입니다._

### 어노테이션 기반으로 개선
<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>반복되는 코드는 유지보수에 좋지않으므로 어노테이션으로 개선</span>

>**🚨 반복되는 코드**
- IndexController에서 세션값을 가져오는 부분
- 다른 컨트롤러와 메소드에서 세션값이 필요하면 그때마다 직접 가져와야함
>
**👉 메소드 인자로 세션값을 받을수 있도록 변경

- config/auth 패키지에 @LoginUser 어노테이션 생성

>- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>@Target(ElementType.PARAMETER)</span>
  - 어노테이션이 생성될 위치 지정
  - PARAMETER로 지정했으므로 메소드의 파라미터로 선언된 객체에서만 이용
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>@interface</span> 
  - 이 파일을 어노테이션 클래스로 지정
  - LoginUser라는 이름의 어노테이션이 생성됨

- 같은 위치에 LoginUserArgumentResolver 생성
  - Login-UserArgumentResolver라는 HandlerMethodArgumentResolver 인터페이스를 구현한 클래스
  - HandlerMethodArgumentResolver는 조건에 맞는 경우 메소드가 있다면 HandlerMethodArgumentResolver의 구현체가 지정한 값으로 해당 메소드의 파라미터로 넘길 수 있음
  
>- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>supportsParameter()</span>
  - 컨트롤러 메서드의 특정 파라미터를 지원하는지 판단
  - 여기서는 파라미터에 @LoginUser 어노테이션이 있고, 파라미터 클래스 타입이 SessionUser.class인 경우 true를 반환
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>resolveArgument()</span>  
  - 파라미터에 전달할 객체를 생성
  - 여기서는 세션에서 객체를 가져옴
  
- 작성된 LoginUserArgumentResolver를 스프링에서 인식하도록 WebMvcConfig에 추가하기 위해 config 패키지에 WebConfig 클래스 생성

**🚨 HandlerMethodArgumentResolver는 항상 WebMvcConfigurer의 addArgumentResolvers()를 통해 추가해야함**

- IndexController의 코드에서 반복된 부분들을 @LoginUser로 개선 

### 세션 저장소로 DB 사용

#### 🚨 문제점 
- 현재 서비스는 애플리케이션을 재실행하면 로그인이 풀린다.
👉 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>세션은 실행되는 WAS 메모리에 저장되고 호출</span>되기 때문에 내장 톰캣처럼 애플리케이션 실행시 실행되는 구조에서는 항상 초기화됨
- 2대 이상의 서버에서 서비스하고 있다면 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>톰캣마다 세션 동기화 설정이 필요</span>하다.

#### 💡 해결방안
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>톰캣 세션 사용</span>
   - 톰캣에 세션이 저장되기 때문에 2대 이상의 WAS가 구동되는 환경에서는 톰캣들 간의 세션 공유를 위한 추가 설정이 필요
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>MySQL과 같은 DB를 세션 저장소로 사용</span>
  - 로그인 요청마다 DB IO가 발생하여 성능상 이슈 발생 가능
  - 보통 로그인 요청이 많이 없는 백오피스, 사내 시스템 용도로 사용
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>Redis, Memcached와 같은 메모리 DB를 세션 저장소로 사용</span>
  - B2C 서비스에서 가장 많이 사용하는 방식
  - 실제 서비스하기 위해서는 Embedded Redis와 같은 방식이 아닌 외부 메모리 서버 필요
  
여기서는 두번째 방식인 DB를 세션 저장소로 사용하는 방법을 선택했다.

#### Spring-session-jdbc 등록
- build.gradle에 의존성 등록
```java
implementation('org.springframework.session:spring-session-jdbc')
```
- application.properties에 세션 저장소를 jdbc로 선택하도록 코드 추가
```java
spring.session.store-type=jdbc
```
- 애플리케이션을 실행하고 로그인을 테스트한 뒤 h2-console로 접속하면 세션을 위한 테이블 2개(SPRING_SESSION, SPRING_SESSION_ATTRIBUTES)가 생성된 걸 볼 수 있음
👉 **JPA로 인해 세션 테이블 자동 생성**
