_스프링부트와 AWS로 혼자 구현하는 웹서비스 책을 정리한 내용입니다._

## 네이버 로그인
- **[네이버 오픈 API](https://developers.naver.com/apps/#/register?api=nvlogin)**로 이동한다.
- 아래처럼 항목을 작성하고 등록한다.
![](https://images.velog.io/images/3hee_11/post/58d0d428-12c8-4170-af89-6bf8e8569689/20220302_163616_1.png)
![](https://images.velog.io/images/3hee_11/post/c927b7b8-a69a-4fa6-951f-932d2baff96a/20220302_163616_2.png)
- 생성된 ClientID와 ClientSecret을 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>application-oauth.properties</span>에 등록한다.

 **네이버에서는 스프링 시큐리티를 공식 지원하지 않기 때문에 Common-OAuth2Provider에서 해준던 값들도 수동 입력해야함**

```java
# registration
spring.security.oauth2.client.registration.naver.client-id=네이버클라이언트ID
spring.security.oauth2.client.registration.naver.client-secret=네이버클라이언트시크릿
spring.security.oauth2.client.registration.naver.redirect-uri={baseUrl}/{action}/oauth2/code/{registrationId}
spring.security.oauth2.client.registration.naver.authorization-grant-type=authorization_code
spring.security.oauth2.client.registration.naver.scope=name,email,profile_image
spring.security.oauth2.client.registration.naver.client-name=Naver

# provider
spring.security.oauth2.client.provider.naver.authorization-uri=https://nid.naver.com/oauth2.0/authorize
spring.security.oauth2.client.provider.naver.token-uri=https://nid.naver.com/oauth2.0/token
spring.security.oauth2.client.provider.naver.user-info-uri=https://openapi.naver.com/v1/nid/me
spring.security.oauth2.client.provider.naver.user-name-attribute=response
```

><span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>user_name_attribute=response</span>
기준이 되는 user_name의 이름을 네이버에서는 response로 해야한다.
👉 **네이버 회원 조회 시 반환되는 JSON 형태 때문**
>
<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>스프링 시큐리티에선 하위 필드를 명시할 수 없음</span>
최상위 필드들만 user_name으로 지정 가능하지만 네이버의 응답값 최상위 필드는 resultCode, message, response다.
👉 스프링 시큐리티에서 인식 가능한 필드는 위 3개 중에 골라야함. 여기서는 response를 usesr_name으로 지정하고 이후 자바 코드로 response의 id를 user_name으로 지정

### 스프링 시큐리티 설정 등록
- OAuthAttributes에 네이버인지 판단하는 코드와 네이버 생성자 추가
- index.mustache에 네이버 로그인 버튼 추가

## 기존 테스트에 시큐리티 적용
- 기존에는 바로 API를 호출할 수 있어서 테스트 코드도 바로 API를 호출하도록 구성되어 있었다. 
- 시큐리티 옵션이 활성화되면서 인증된 사용자만 API를 호출할 수 있게 됐다.
- 기존의 API 테스트 코드들이 모두 인증에 대한 권한을 받지 못했으므로 테스트 코드마다 인증한 사용자가 호출한 것처럼 작동하도록 수정해야한다.

### 문제 1. CustomOAuth2UserService를 찾을 수 없음
CustomOAuth2UserService를 생성하는데 필요한 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>소셜 로그인 관련 설정값들이 없기 때문에</span> 발생

#### 🤔 application-oauth.properties에 설정값을 추가했는데..?
<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>src/main</span> 환경과 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>src/test</span> 환경의 차이 때문이다. 둘은 본인만의 환경 구성을 가진다. 

main에 있는 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>application.properties</span>가 테스트 코드 수행 시 적용되는 이유는 test에 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>application.properties</span>가 없으면 main의 설정을 가져오기 때문이다.

**하지만 자동으로 가져오는 범위는 application.properties까지이다. **

💡 해결하기 위해 테스트 환경을 위한 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>application.properties</span>을 만든다.

### 문제 2. 302 Status Code
스프링 시큐리티 설정때문에  인증되지 않은 사용자의 요청은 이동시키기 때문에 발생

**👉 임의로 인증된 사용자를 추가하여 API만 테스트할 수 있도록 설정**

- 스프링 시큐리티 테스트를 위한 여러 도구를 지원하는 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>spring-security-test</span>를 build.gradle에 추가
```java
testImplementation('org.springframework.security:spring-security-test')
```
- PostApiControllerTest의 테스트 메소드에 임의의 사용자 인증 추가

><span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>@WithMockUser(roles="USER")</span>
- 인증된 모의 사용자를 만들어서 사용
- roles에 권한을 추가할 수 있음
- 이 어노테이션으로 인해 ROLE_USER 권한을 가진 사용자가 API를 요청하는 것과 동일한 효과를 가짐
>
**🚨 @WithMockUser는 MockMvc에서만 작동**
>
PostsApiControllerTest는 @SpringBootTest로만 되어 있기 때문에 작동하지 않는다. 그래서 @SpringBootTest가 MockMvc를 사용하도록 코드를 변경한다.
>
<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>@Before</span>
- 매번 테스트가 시작되기 전에 MockMvc 인스턴스 생성
>
<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>mvc.perform</span>
- 생성된 MockMvc를 통해 API를 테스트한다.
- Body 영역은 문자열로 표현하기 위해 ObjectMapper를 통해 문자열 JSON으로 변환한다.

### 문제 3 @WebMvcTest에서 CustomOAuth2UserService를 찾을 수 없음
@WebMvcTest는 WebSecurityConfigurerAdapter, WebMvcConfigurer을 비롯한 @ControllerAdvice, @Controlelr를 읽는다.
👉 **@Repository, @Service, @Component는 스캔 대상이 아님**

<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>SecurityConfig는 읽었으나 SecurityConfig 생성을 위해 필요한 CustomOAuth2UserService는 읽을 수가 없어 발생</span>

**💡 이를 해결하기 위해 스캔 대상에서 SecurityConfig를 제거**

HelloControllerTest도 @WithMockUser을 사용해 가짜로 인증된 사용자를 생성한다.

다시 테스트해보면 @EnableJpaAuditing로 인해 IllegalArgumentException이 발생한다.
👉 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>@EnableJpaAuditing을 사용하려면 하나 이상의 @Entity 클래스가 필요하기 때문</span>

@EnableJpaAuditing가 @SpringBootApplication과 함께 있어서 @WebMvcTest에서도 스캔하게 됨
👉 **@EnableJpaAuditing과 @SpringBootApplication 분리**

- Application.java에서 @EnableJpaAuditing를 제거
- config 패키지에 JpaConfig를 생성하여 @EnableJpaAuditing 추가
