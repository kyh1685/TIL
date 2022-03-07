_스프링부트와 AWS로 혼자 구현하는 웹서비스 책을 정리한 내용입니다._

## Spring Security
- <span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>인증(Authentication)</span>과 <span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>인가(Authorization)</span> 기능을 가진 프레임워크
- 사실상 스프링 기반의 애플리케이션에서는 보안을 위한 표준
- 확장성을 고려해 다양한 요구사항을 손쉽게 추가, 변경 가능

>**🚨 직접 로그인을 구현할 때 구현해야하는 것**
- 로그인 시 보안
- 회원가입 시 이메일, 전화번호 인증
- 회원정보 변경
- 비밀번호 찾기/변경
>
**👉 OAuth 로그인 구현 시 구글, 네이버 등에 맡기니까 서비스 개발에 집중 가능**

### 스프링부트 1.5 VS 2.0
- 스프링 부트 1.5에서 2.0으로 넘어가면서 OAuth2 연동 방법이 크게 변경됐다.
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>spring-security-oauth2-autoconfigure</span> 라이브러리 사용시 2.0에서도 1.5에서 쓰던 설정을 그대로 사용할 수 있다.
- 1.5 방식에서는 url 주소를 모두 명시해야 하지만 2.0 방식에서는 client 인증 정보만 입력하면 된다.
👉 CommonOAuth2Provider라는 enum이 추가되어 구글 등의 설정값을 제공

>**🙋‍♀️ 여기서는 2.0 방식인 Spring Security Oauth2 Client 라이브러리를 사용**
>
- 스프링 팀에서 신규 기능은 oauth2 라이브러리만 지원하겠다고 선언
- 스프링부트용 라이브러리(starter) 출시
- 기존 방식은 확장 포인트가 적절하게 오픈되어 있지 않아 직접 상속하거나 오버라이딩 해야하지만 신규 라이브러리는 확장 포인트를 고려해 설계된 상태

### 구글 서비스 등록
구글 서비스에서 발급된 인증 정보(clientId와 clientSecret)를 통해 로그인 기능과 소셜 서비스 기능을 사용할 수 있으니 먼저 발급받아야 한다.

- [구글 클라우드 플랫폼](https://console.cloud.google.com)에 접속해 상단의 프로젝트 선택을 클릭하고 새 프로젝트를 생성한다(프로젝트 이름은 등록될 서비스의 이름을 입력)
![](https://images.velog.io/images/3hee_11/post/4abf0135-0fa2-4d0f-a542-1d8328d4d2f8/20220228_184027_3.png)
- 생성이 완료된 프로젝트를 선택하고 왼쪽 메뉴에서 API 및 서비스 카테고리로 이동한다.
![](https://images.velog.io/images/3hee_11/post/e2fca8b6-f48c-4de6-9f68-9361756154e1/20220228_184027_4.png)
- 사이드바에서 사용자 인증 정보를 클릭하고 오른쪽 화면에서 사용자 인증 정보 만들기를 클릭한다.
![](https://images.velog.io/images/3hee_11/post/408dce1d-bfdf-4180-9243-62d690fa2716/20220228_184027_5.png)
- 사용자 인증 정보에 여러 메뉴가 있는데 이중 OAuth 클라이언트 ID를 선택한다.
![](https://images.velog.io/images/3hee_11/post/0017118f-b901-43aa-93ef-430d45c0784f/20220228_184027_6.png)
- 클라이언트 ID가 생성되기 전에 동의 화면 구성이 필요하므로 안내에 따라 동의 화면 구성을 클릭한다.
- 동의 화면에는 3개의 단계가 있는데 이중 OAuth 동의 화면에서 앱 이름과 사용자 메일을 작성한다.
- 그 다음 범위 단계에서 범위 추가 또는 삭제를 클릭해  email, profile, openId를 추가해준다.
![] (https://images.velog.io/images/3hee_11/post/e18bf74b-7894-45bd-a7c3-93d875ff2ac1/20220228_184027_8.png)
- 다음 단계인 테스트 사용자에서 ADD USERS를 클릭해 나의 이메일을 추가한다.
![](https://images.velog.io/images/3hee_11/post/c0b80ca8-e7bb-4506-80ac-7b585f92a046/20220228_184027_10.png)
- 동의 화면 구성이 끝나면 저장을 하고 OAuth 클라이언트 ID만들기 화면으로 이동한다.
- 애플리케이션 유형은 웹 애플리케이션으로 선택하고 프로젝트 이름을 등록한다.
![](https://images.velog.io/images/3hee_11/post/a5759fbc-e839-47a9-a7a4-4818dc552998/20220228_184027_12.png)
- 화면 아래 승인된 리디렉션에 아래와 같이 입력한 다음 저장 버튼을 누르면 생성된 클라이언트 정보를 볼 수 있다.
![](https://images.velog.io/images/3hee_11/post/8ee3a6e5-9bbd-4995-bb78-2d78e8ec0460/20220228_184027_13.png)

> **💡 승인된 리디렉션 URI**
- 서비스에서 파라미터로 인증 정보를 주었을 때, 인증이 성공하면 구글에서 리다이렉트할 URL
- 스프링부트 2.0의 시큐리티에서는 기본적으로 {도메인}/login/oauth2/code/{소셜서비스코드}로 리다이렉트 URL을 지원
- 사용자가 따로 리다이렉트 URL을 지원하는 Controller를 만들 필요없음
👉 시큐리티에서 이미 구현해놓은 상태
>
_현재 개발 단계이므로 위처럼 등록함 AWS에 배포하게 되면 주소 추가해야함_

- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>src/main/resources</span>에 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>application-oauth.properties</span> 파일 생성
- 생성한 파일에 클라이언트 ID(clientId)와 클라이언트 보안 비밀(clientSecret) 코드를 아래와 같이 등록
```java
spring.security.oauth2.client.registration.google.client-id=클라이언트 ID
spring.security.oauth2.client.registration.google.client-secret=클라이언트 보안 비밀
spring.security.oauth2.client.registration.google.scope=profile,email
```

> **💡 scope=profile, email**
>
- scope의 기본값이 openid, profile, email이기 때문에 많은 예제에서 scope를 별도로 등록하지 않음
- 여기서 profile, email을 등록한 이유는 openid라는 scope가 있으면 Open Id Provider로 인식하기 떄문
- 그렇게 되면 OpenId Provider인 서비스(구글)과 그렇지 않은 서비스(네이버, 카카오)로 나눠서 각각 OAuth2Service를 만들어야함
>
👉 하나의 OAuth2Service로 사용하기 위해 일부러 openid scope를 빼고 등록

- 스프링부트에서는 properties의 이름을 application-xxx.properties로 만들면 xxx라는 이름의 profile이 생성되어 이를 통해 관리할 수 있음
👉 profile=xxx라는 식으로 호출하면 해당 properties의 설정을 가져옴
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>application.properties</span>에서 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>application-oauth.properties</span>를 포함하도록 <span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>spring.profiles.include=oauth</span> 추가

#### .gitignore 등록
- 클라이언트 ID와 클라이언트 보안 비밀은 깃허브에 올라가면 안됨 
👉 .gitignore에 <span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>application-oauth.properties</span> 코드 추가

- 커밋했을 때 커밋 파일 목록에 application-oauth.properties가 나오지 않아야함
- 만약 나온다면 git의 캐시 문제이므로 아래 명령어로 캐시 내용을 전부 삭제한 후 다시 커밋
```java
git rm -r --cached .
git add .
git commit -m "fixed untracked files"
```

### 구글 로그인 연동
- domain → user 하위에 사용자 정보를 담당할 도메인 User 클래스 생성 

><span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>@Enumerated(EnumType.STRING)</span>
- JPA로 DB에 저장할 때 Enum 값을 어떤 형태로 저장할지 결정
- 기본적으로 int로 된 숫자가 저장됨
👉 DB로 확인할 때 그 값이 무슨 코드를 의미하는지 불분명해짐
- 그래서 문자열로 저장될 수 있도록 선언

- user 패키지 하위에 사용자의 권한을 관리할 Enum 클래스 Role 생성
- **스프링 시큐리티에서는 권한 코드에 항상 ROLE_이 앞에 있어야함**
예) ROLE_GUEST, ROLE_USER 등
- user 패키지에 User의 CRUD를 책임질 UserRepository 인터페이스 생성

><span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>findByEmail</span>
- 소셜 로그인으로 반환되는 값 중 email을 통해 가입 여부를 판단하기 위함

### 스프링 시큐리티 설정

#### build.gradle에 스프링 시큐리티 관련 의존성 추가
```java
implementation('org.springframework.boot:spring-boot-starter-oauth2-client')
```

><span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>spring-boot-starter-oauth2-client</span>
- 소셜 로그인 등 클라이언트 입장에서 소셜 기능 구현시 필요한 의존성
- spring-security-oauth2-client와 spring-security-oauth2-jose를 기본으로 관리

#### OAuth 라이브러리를 이용한 소셜 로그인 설정코드 추가
- 시큐리티 관련 클래스를 담을 config.auth 패키지 생성 
- SecurityConfig 클래스 생성

> **📌 설명**
>
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>@EnableWebSecurity</span>
  -  Spring Security 설정들을 활성화
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>csrf().disable().headers().frameOptions().disable()</span>
  - h2-console 화면을 사용하기 위해 해당 옵션들을 disable
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>authorizeRequests</span>
  - URL별 권한 관리를 설정하는 옵션의 시작점
  - 선언 되어야만 antMatchers 옵션 사용 가능
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>andMatchers</span>
  - 권한 관리 대상을 지정하는 옵션
  - URL, HTTP 메소드별 관리 가능
  - "/"등 지정된 URL들은 permitAll()을 통해 전체 열람 권한 부여
  - "/api/v1/**" 주소를 가진 API는 USER 권한을 가진 사람만 가능하도록 함
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>anyRequest</span>
  - 설정된 값들 이외 나머지 URL을 나타냄
  - 여기서는 authenticated()를 추가해 나머지 URL들을 모두 인증된 사용자에게만 허용하게 함
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>logout().logoutSuccessUrl("/")</span>
  - 로그아웃 기능에 대한 여러 설정의 시작점
  - 로그아웃 성공시 / 주소로 이동
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>oauth2Login</span>
  - OAuth2 로그인 기능 설정의 시작점
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>userInfoEndpoint</span>
  - OAuth2 로그인 성공 이후 사용자 정보를 가져올 때 설정들을 담당
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>userService</span>
  - 소셜 로그인 성공 시 후속 조치를 진행할 서비스 UserService의 인터페이스의 구현체를 등록
  - 리소스 서버(소셜 서비스들)에서 사용자 정보를 가져온 상태에서 추가로 진행하고자 하는 기능을 명시 
  
- customOAuth2UserService 클래스 생성

>**📌 설명**
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>registraionId</span>
  - 현재 로그인 진행 중인 서비스를 구분하는 코드
  - 이후 다른 로그인 서비스가 추가되면 어떤 서비스인지 구분해야하기 위함
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>userNameAttributeName</span>
  - OAuth2 로그인 진행 시 키가 되는 필드값(=Primary Key)
  - 구글은 기본으로 코드(sub)를 지원하지만 네이버, 카카오 등은 지원하지 않음
  - 이후 네이버, 구글 로그인을 동시 지원할 때 사용
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>OAuthAttributes</span>
  - OAuth2UserService를 통해 가져온 OAuth2User의 attribute를 담을 클래스
  - 이후 다른 소셜 로그인에도 이 클래스를 사용
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>SessionUser</span>
  - 세션에 사용자 정보를 저장하기 위한 Dto 클래스

- auth/dto 패키지에 OAuthAttributes 클래스 생성

>**📌 설명**
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>of()</span>
  - OAuth2User에서 반환하는 사용자 정보는 Map이기 때문에 값 하나하나를 변환해야함
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>toEntity()</span>
  - User 엔티티 생성
  - OAuthAttributes에서 엔티티를 생성하는 시점은 처음 가입할 때
  - 가입할 때 기본 권한을 GUEST로 주기 위해서 role 빌더값은 Role.GUEST 사용
  
- config/auth/dto 패키지에 SessionUser 클래스 추가
  - 인증된 사용자 정보만 필요하므로 name, email, picture만 필드로 선언
  
>**🤔 왜 User 클래스 대신 SessionUser 클래스를 사용할까?**
- User 클래스는 엔티티이므로 언제 다른 엔티티와 관계가 형성될지 모른다.
- 만약 @OneToMany, @ManyToOne등 자식 엔티티를 가지고 있다면 직렬화 대상에 자식들까지 포함되니 성능 이슈, 부수 효과가 발생할 확률이 높다.
>
**직렬화 기능을 가진 세션 Dto를 추가로 만드는 것이 운영 및 유지보수에 좋음**

### 로그인 테스트
>**📌 설명**
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>{{#userName}}</span>
  - 머스테치는 if문을 제공하지 않고 true, false 여부만 판단함
  - 그래서 머스테치에는 항상 최종값을 넘겨야함
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>a href="/logout"</span>
  - 스프링 시큐리티에서 기본적으로 제공하는 로그아웃 URL
  👉 개발자가 별도로 URL에 해당하는 컨트롤을 만들 필요없음 
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>{{^userName}}</span>
  - 머스테치에서는 해당 값이 존재하지 않는 경우에 ^사용
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>a href="/oauth2/authorization/google"</span>
  - 스프링 시큐리티에서 기본적으로 제공하는 로그인 URL
  
- index.mustache에서 userName을 사용할 수 있도록 IndexController에 userName을 model에 저장하는 코드 추가

>**📌 설명**
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>httpSession.getAttribute()</span>
  - CustomOAuth2UserService에서 로그인 성공시 세션에 SessionUser을 저장하도록 구성했으므로 getAttribute()를 통해서 값을 가져올 수 있음
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>if(usesr != null)</span>
  - 세션이 저장된 값이 있을 때만 model에 userName으로 등록
  - 세션이 값이 없으면 model엔 아무런 값이 없는 상태이니 로그인 버튼 안보임
  
현재 글쓰기를 진행하면 403(권한 거부) 에러가 발생한다. h2-console로 가서 사용자의 role을 USER로 변경하고 로그아웃한 후 다시 로그인하면 글이 정상적으로 등록된다.
