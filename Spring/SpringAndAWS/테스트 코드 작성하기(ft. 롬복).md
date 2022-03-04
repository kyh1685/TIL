_스프링부트와 AWS로 혼자 구현하는 웹서비스 책을 정리한 내용입니다._

## 테스트 코드
TDD와 단위 테스트는  <span style='padding: 0px 7px; background-color: #F7DDBE; border-radius: 4px'>다른 개념</span>이다.

> **💡 TDD**
테스트가 주도하는 개발
>
실패하는 테스트 먼저 작성 → 테스트가 통과하는 코드 작성 → 코드 리팩토링

### 단위 테스트
단위 테스트는 TDD의 첫 번째 단계인 <span style='padding: 0px 7px; background-color: #F7DDBE; border-radius: 4px'>기능 단위의 테스트 코드를 작성</span>하는 것이다.

#### 🤔 단위 테스트를 왜 작성해야 할까?
- 개발 초기단계에 문제를 발견할 수 있음
- 기능에 대한 불확실성 감소
- 시스템에 대한 실제 문서 제공 → 단위 테스트 자체가 문서로 사용됨
- 리팩토링, 라이브러리 업그레이드 등을 할 때, 기존 기능이 정상 작동하는지 확인 가능

### 테스트 코드 작성하기

#### 패키지와 메인 클래스 생성
<span style='padding: 0px 7px; background-color: #F7DDBE; border-radius: 4px'>✔ com.jojoldu.book.springboot</span> 일반적으로 패키지명은 웹사이트 주소의 역순이다.

> - <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@SpringBootApplication</span>
스프링 부트 자동 설정, 스프링 빈 읽기와 생성 자동 설정
@SpringBootApplication이 있는 위치부터 설정을 읽기 때문에 **항상 프로젝트 최상단에 위치**해야 한다.
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>SpringApplication.run</span>
내장 WAS(애플리케이션 실행 시 내부에서 WAS를 실행하는 것)를 실행
스프링부트는 내장 WAS 권장 → 언제 어디서나 같은 환경에서 배포할 수 있기 때문

#### 하위 패키지와 Controller 생성
기존 패키지 밑에 컨트롤러와 관련된 클래스를 담을 web 패키지와 Controller 생성

>- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@RestController</span>
컨트롤러를 JSON을 반환하는 컨트롤러롤 만들어줌
@ResponseBody를 각 메서드마다 선언했던 걸 한번에 사용할 수 있게 해줌
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@GetMapping</span>
Http 메서드인 Get의 요청을 받을 수 있는 API를 만들어줌
@RequestMapping(method=RequestMethod.GET)과 같음

#### 테스트 클래스 생성
>- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@RunWith(SpringRunner.class)</span>
  - 테스트 진행 시 JUnit에 내장된 실행자 외에 다른 실행자를 실행시킴
  - 여기서는 SpringRunner라는 스프링 실행자를 사용
  - 즉, 스프링부트 테스트와 JUnit 사이 연결자 역할
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@WebMvcTest</span>
  - 여러 스프링 테스트 어노테이션 중, Web(Spring MVC)에 집중할 수 있는 어노테이션
  - 선언할 경우 @Controller, @ControllerAdvice 사용 가능
  - 단, @Service, @Component, @Repository는 사용 불가
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@Autowired</span>
  - 스프링이 관리하는 빈을 주입받음
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>private MockMvc mvc</span>
  - 웹 API를 테스트할 때 사용
  - 스프링 MVC 테스트의 시작점으로 GET, POST 등 API 테스트 가능
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>mvc.perform(get("/hello"))</span>
  - MockMvc를 통해 /hello 주소로 GET 요청
  - 체이닝이 지원되어 아래와 같은 여러 검증을 이어서 할 수 있음
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>andExpect(status().isOk())</span>
  - mvc.perform 결과 검증
  - HTTP Header의 Status(200,400 등) 검증
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>andExpect(content().string(hello))</span>
  - mvc.perform 결과 검증
  - 응답 본문의 내용 검증
  
#### 👇 테스트가 오래 걸리고 실패한다면
![](https://images.velog.io/images/3hee_11/post/b1a538cd-2c2d-43fb-bc24-26704d51b18e/image.png)

## 롬복
자바 개발 시 자주 사용되는 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>Getter</span>, <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>Setter</span>, <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>기본 생성자</span>, <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>toString</span> 등을 어노테이션으로 자동 생성해준다.  

### 롬복 설치하기
- build.gradle에 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>implementation('org.projectlombok:lombok')</span> 추가 후 새로고침해서 라이브러리를 내려받는다.
- Setting → Plugins의 Marketplace 탭에서 lombok 검색 후 install 
- 재시작 후 Setting → Build, Execution ~ → Annotation Processor에서 체크박스 체크
![](https://images.velog.io/images/3hee_11/post/5bdc32f0-160c-4d8d-9141-f10950031777/image.png)

### 기존 코드 롬복으로 전환
- web 패키지 밑에 dto 패키지 추가
- dto 패키지에 HelloResponseDto 생성

>- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@Getter</span>
선언된 모든 필드의 get메소드 생성
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@RequiredArgsConstructor</span>
- 선언된 모든 **final 필드**가 포함된 생성자 생성

### 테스트
기존 패키지에 dto 패키지 추가 후 HelloResponseDtoTest 클래스 생성

>- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>assertThat</span>
  - assertj라는 테스트 검증 라이브러리의 검증 메소드
  - 검증하고 싶은 대상을 메소드 인자로 받음
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>isEqualTo</span>
  - assertj의 동등 비교 메서드
  - assertThat에 있는 값과 isEqualTo의 값을 비교해 일치할 때만 성공

#### 🤔 Junit대신 assertj의 assertThat는를 사용한 이유는?
- CoreMatchers와 달리 추가적으로 라이브러가 필요하지않다.
- 자동완성이 더 확실하게 지원된다.
  
### HelloController 리팩토링
> <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@RequestParam</span> 
- 외부에서 API로 넘긴 파라미터를 가져옴
- 여기서는 외부에서 name(@RequestParam("name"))이란 이름으로 넘긴 파라미터를 메소드 파라미터 name(String name)에 저장

### 테스트
> - <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>param</span> 
  - API 테스트를 할 떄 사용될 요청 파라미터를 설정
  - **값은 String만 허용**
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>jsonPath</span> 
  - JSON 응답값을 필드별로 검증할 수 있는 메서드
  - $를 기준으로 필드명을 명시
