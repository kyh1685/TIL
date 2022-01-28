# 빈 생명주기 콜백

_김영한 강사님의 스프링 핵심 원리 - 기본편 강의를 정리한 내용입니다._

✔ 스프링 빈은 객체를 생성한 다음 의존관계 주입을 하는 라이프 사이클을 가지고 있다. 
✔ 초기화 작업은 **의존관계 주입이 완료된 시점에 호출**해야 한다.

🤔 개발자가 의존관계 주입이 완료된 시점을 어떻게 알 수 있을까? 
📌 스프링은 의존관계 주입이 완료되면 **스프링 빈에게 콜백 메소드를 통해 초기화 시점을 알려주는** 기능을 제공한다. 또한 **스프링 컨테이너가 종료되기 직전에 소멸 콜백**을 준다.

#### 스프링 빈의 이벤트 라이프사이클
스프링 컨테이너 생성 → 스프링 빈 생성 → 의존관계 주입 → 초기화 콜백 → 사용  → 소멸전 콜백 → 스프링 종료

- 초기화 콜백: 빈이 생성되고, 빈의 의존관계 주입이 완료된 후 호출
- 소멸전 콜백: 빈이 소멸되기 직전에 호출

### 인터페이스 InitializingBean, DisposableBean
```java
public class NetworkClient implements InitializingBean, DisposableBean {
	
    // 초기화를 지원한다.
 	@Override
 	public void afterPropertiesSet() throws Exception {
 	    connect();
 	    call("초기화 연결 메시지");
 	}
 	
    // 소멸 지원
 	@Override
 	public void destroy() throws Exception {
 	    disConnect();
 	}
 
}
```
🚨 **단점**
✔ 해당 코드가 스프링 전용 인터페이스에 의존한다.
✔ 초기화, 소멸 메서드의 이름을 변경할 수 없다.
✔ 코드를 고칠 수 없는 외부 라이브러리에 적용할 수 없다.

### 빈 등록 초기화, 소멸 메서드 지정
```java
public class NetworkClient {

 	public void init() {
 	    System.out.println("NetworkClient.init");
 	    connect();
 	    call("초기화 연결 메시지");
 	}
    **
 	public void close() {
 	    System.out.pr**intln("NetworkClient.close");
 	    disConnect();
 	}
    
}

@Configuration
static class LifeCycleConfig {

     @Bean(initMethod = "init", destroyMethod = "close")
     public NetworkClient networkClient() {
     	NetworkClient networkClient = new NetworkClient();
     	networkClient.setUrl("http://hello-spring.dev");
        
     	return networkClient;
     }
     
}
```
💡 **특징**
✔ 메서드 이름을 변경할 수 있다.
✔ 스프링 빈이 스프링 코드에 의존하지 않는다.
✔ 코드가 아니라 설정 정보를 사용하기 때문에 코드를 고칠 수 없는 외부 라이브러리에도 초기화, 종료 메서드를 적용할 수 있다.

⭐ **종료 메서드 추론**
- @Bean의 destroyMethod 는 기본값이 (inferred) (추론)으로 등록되어 있다.
- 추론 기능은 close , shutdown 라는 이름의 종료 메서드를 자동으로 호출해준다. 
- 추론 기능을 사용하기 싫으면 destroyMethod="" 처럼 빈 공백을 지정하면 된다.

### 애노테이션 @PostConstruct, @PreDestroy
```java
public class NetworkClient {

    @PostConstruct
    public void init() {
        System.out.println("NetworkClient.init");
        connect();
        call("초기화 연결 메시지");
    }
    
    @PreDestroy
    public void close() {
        System.out.println("NetworkClient.close");
        disConnect();
    }
 
}

@Configuration
static class LifeCycleConfig {
 
     @Bean
     public NetworkClient networkClient() {
         NetworkClient networkClient = new NetworkClient();
         networkClient.setUrl("http://hello-spring.dev");
         
         return networkClient;
     }
 
}
```
💡 **특징**
- 스프링에서 가장 권장하는 방법이다.
- 패키지 맨 앞에 javax는 스프링에 종속적인 기술이 아니라 자바 표준이라는 뜻이다. 따라서 스프링이 아닌 다른 컨테이너에서도 동작한다.
- 컴포넌트 스캔과 잘 어울린다.

🚨 **단점**
- 외부 라이브러리에 적용할 수 없다.

📌 **외부 라이브러리를 초기화, 종료해야 하면 @Bean의 initMethod , destroyMethod를 그 밖에는 @PostConstruct, @PreDestroy 애노테이션을 사용하자!**
