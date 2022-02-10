# 빈 스코프

_김영한 강사님의 스프링 핵심 원리 - 기본편 강의를 정리한 내용입니다._

## 빈 스코프
빈이 존재할 수 있는 범위로 **스프링 빈은 기본적으로 싱글톤 스코프로 생성**된다.

- **싱글톤** : 스프링 컨테이너의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프
- **프로토타입** : 스프링 컨테이너가 프로토타입 빈의 생성과 의존관계 주입까지만 관여하고 더는 관리하지 않는 매우 짧은 범위의 스코프
- **웹 관련 스코프** : request, session, application

```java
// 컴포넌트 스캔 자동 등록
@Scope("prototype")
@Component
public class HelloBean {}

// 수동 등록
@Scope("prototype")
@Bean
PrototypeBean HelloBean() {
 return new HelloBean();
}
```

### 프로토타입 스코프
프로토타입 스코프를 스프링 컨테이너에 조회하면 **항상 새로운 인스턴스를 생성해서 반환한다.**

📌 스프링 컨테이너는 프로토타입 빈을 생성하고, 의존관계 주입, 초기화까지만 처리하고 생성된 프로토타입 빈을 관리하지 않는다. 
👉 @PreDestroy 같은 종료 메서드가 호출되지 않음

#### 🚨 프로토타입 스코프를 싱글톤 빈과 함께 사용시 문제점
<img src="https://images.velog.io/images/3hee_11/post/c25de543-f63d-4041-bd0b-f70d3aa8865b/image.png" width="90%">

1. clientBean이 스프링 컨테이너 생성 시점에 같이 생성되고, 의존관계 주입이 일어나 프로토타입 빈 요청
2. 스프링 컨테이너가 프로토타입 빈을 생성해서 clientBean에 반환
3. clientBean은 프로토타입 빈을 내부 필드에 보관(정확히는 참조값)
4. A가 clientBean을 요청하고 싱글톤이므로 같은 clientBean 반환
5. A가 clientBean.logic() 호출
6. clientBean이 prototypeBean의 addCount()를 호출
7. 프로토타입 빈의 count 값이 1로 증가
8. B가 clientBean을 요청해서 받고 A와 같은 clientBean 반환
👉 **clientBean 내부에 있는 프로토타입 빈은 이미 과거에 주입이 끝난 빈으로 사용 할 때마다 새로 생성되지 않음**
9. B가 clientBean.logic() 호출하고 6, 7번과 같은 과정이 일어나 값이 2가 됨

#### 💡 Provider 사용해서 항상 새로운 프로토타입 빈 생성하기
- **ObjectProvider** : 지정한 빈을 컨테이너에서 대신 찾아주는 DL 서비스를 제공
  - ObjectFactory 상속, 옵션, 스트림 처리 등 편의 기능이 많고, 별도의 라이브러리 필요 없음, 스프링에 의존
```java
@Autowired
private ObjectProvider<PrototypeBean> prototypeBeanProvider;

public int logic() {
    //getObject() : 스프링 컨테이너를 통해 해당 빈을 찾아서 반환(DL)
    PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
    prototypeBean.addCount();
    int count = prototypeBean.getCount();
    return count;
}
```
- **JSR-330 Provider** : javax.inject.Provider 라는 JSR-330 자바 표준을 사용
  - 별도의 라이브러리가 필요, 자바 표준이므로 스프링이 아닌 다른 컨테이너에서도 사용 가능
```java
//implementation 'javax.inject:javax.inject:1' gradle 추가 필수
@Autowired
private Provider<PrototypeBean> provider;

public int logic() {
    // get() : 스프링 컨테이너를 통해 해당 빈을 찾아서 반환(DL)
    PrototypeBean prototypeBean = provider.get();
    prototypeBean.addCount();
    int count = prototypeBean.getCount();
    return count;
}
```

📌 프로토타입 빈은 사용할 때 마다 의존관계 주입이 완료된 새로운 객체가 필요할 때 사용
📌 ObjectProvider , JSR330 Provider 등은 프로토타입 뿐만 아니라 DL이 필요한 경우 언제든지 사용 가능

### 웹 스코프
- 웹 스코프는 웹 환경에서만 동작한다.
- 프로토타입과 다르게 스프링이 해당 스코프의 종료시점까지 관리한다.
👉 종료 메서드가 호출됨

#### 웹 스코프 종류
- **request** : HTTP 요청 하나가 들어오고 나갈 때 까지 유지되는 스코프, 각각의 HTTP 요청마다 별도의 빈 인스턴스가 생성되고 관리됨
- **session** : HTTP Session과 동일한 생명주기를 가지는 스코프
- **application** : 서블릿 컨텍스트와 동일한 생명주기를 가지는 스코프
- **websocket** : 웹 소켓과 동일한 생명주기를 가지는 스코프

#### request 스코프
- 웹 스코프는 웹 환경에서만 동작하므로 web 환경이 동작하도록 라이브러리를 추가
```java
// build.gradle에 web 라이브러리 추가
implementation 'org.springframework.boot:spring-boot-starter-web'
```
> 💡 spring-boot-starter-web 라이브러리를 추가하면 스프링 부트는 내장 톰켓 서버를 활용해서 웹 서버와 스프링을 함께 실행시킨다.
💡 웹 라이브러리가 없으면 AnnotationConfigApplicationContext, 있으면 AnnotationConfigServletWebServerApplicationContext를 기반으로 애플리케이션을 구동한다.

 #### request 스코프를 활용한 추가 기능 개발
```java
@Component
// HTTP 요청 당 하나씩 생성, HTTP 요청이 끝나면 소멸
@Scope(value = "request")
public class MyLogger {

    private String uuid;
    private String requestURL;
    
    // 빈 생성 시점에는 requestURL을 알 수 없으므로 외부에서 setter로 입력 받음
    public void setRequestURL(String requestURL) {
        this.requestURL = requestURL;
    }
 	
    public void log(String message) {
        System.out.println("[" + uuid + "]" + "[" + requestURL + "] " + message);
    }
 	
    // 빈이 생성되는 시점에 @PostConstruct를 사용해서 uuid를 생성후 저장
    @PostConstruct public void init() {
        uuid = UUID.randomUUID().toString();
        System.out.println("[" + uuid + "] request scope bean create:" + this);
    }
    
    // 소멸되는 시점에 @PreDestroy로 종료 메시지 출력
    @PreDestroy
    public void close() {
        System.out.println("[" + uuid + "] request scope bean close:" + this);
    }
}

@Controller
@RequiredArgsConstructor
public class LogDemoController { 

    private final LogDemoService logDemoService;
    private final MyLogger myLogger;
    
    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
        String requestURL = request.getRequestURL().toString();
        myLogger.setRequestURL(requestURL);
        myLogger.log("controller test");
        logDemoService.logic("testId");
        return "OK";
    }
}

@Service
@RequiredArgsConstructor
public class LogDemoService {
    private final MyLogger myLogger;
    
    public void logic(String id) {
        myLogger.log("service id = " + id);
    }
}
```

🤔 **request scope를 사용하지 않고 파라미터로 넘긴다면?**
- 파라미터가 많아서 지저분해짐
- requestURL 같은 웹과 관련된 정보가 관련없는 서비스 계층까지 넘어가게 됨 
👉 웹과 관련된 부분은 컨트롤러까지만 사용해야 함
👉 서비스 계층은 웹 기술에 종속되지 않고 순수하게 유지하는 것이 유지보수 관점에서 좋음

📌 request scope를 사용해서 파라미터로 넘기지 않고, 빈(MyLogger)의 멤버변수에 저장해서 코드와 계층을 깔끔하게 유지하자 

#### 🚨 에러 발생
<img src="https://images.velog.io/images/3hee_11/post/0307107f-6444-4eb0-9a5b-461393aaaa47/image.png">

🚨 스프링 애플리케이션을 실행하는 시점에 싱글톤 빈은 생성해서 주입이 가능하지만, **request 스코프 빈은 실제 요청이 와야 생성할 수 있기 때문에 아직 생성되지 않는다.** 
👉 **Provider 혹은 프록시 방식 사용**

- **Provider** 
  - MyLogger대신 ObjectProvider< MyLogger > 사용
  - myLoggerProvider.getObject()를 호출해 MyLogger를 얻기 
  👉 **getObject() 호출 시점까지 request scope 빈 생성 지연**
- **프록시 방식**
  - 다른 코드 수정없이 MyLogger에   proxyMode 넣기
```java
@Component
//  MyLogger의 가짜 프록시 클래스를 만들어두고 다른 빈에 미리 주입
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class MyLogger {
}
```
> 💡 proxyMode = ScopedProxyMode.TARGET_CLASS를 설정하면 스프링 컨테이너는 CGLIB라는 바이트코드를 조작하는 라이브러리를 사용해서 MyLogger를 상속받은 가짜 프록시 객체를 생성 후 등록한다.
💡 가짜 프록시 객체는 요청이 오면 그때 내부에서 진짜 빈을 요청하는 위임 로직이 들어있다.
