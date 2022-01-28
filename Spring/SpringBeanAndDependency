# 스프링빈과 의존관계

_김영한 강사님의 스프링 입문 강의를 정리한 내용입니다._

### 컴포너트 스캔 원리

- @Component 어노테이션이 있으면 스프링빈으로 자동 등록된다.
- @Component를 포함하는 Controller, Service, Repository도 자동으로 등록된다.

### 스프링빈을 등록하는 2가지 방법
#### 1. 컴포넌트 스캔과 자동 의존관계 설정
```java
@Controller
public class MemberController {
   private final MemberService memberService;
   
   @Autowired
   public MemberController(MemberService memberService) { 
   	this.memberService = memberService;
   }
}
```
- 생성자에 @Autowired 가 있으면 스프링이 연관된 객체를 스프링 컨테이너에서 찾아서 넣어준다. 이렇게 객체 의존관계를 외부에서 넣어주는 것을 DI (Dependency Injection), 의존성 주입이라 한다.
- 생성자가 1개만 있으면 @Autowired 는 생략할 수 있다.
- 스프링은 스프링 컨테이너에 스프링 빈을 등록할 때, 기본으로 싱글톤으로 등록한다. 따라서 같은 스프링 빈이면 모두 같은 인스턴스다. 
-  DI에는 필드 주입, setter 주입, 생성자 주입 이렇게 3가지 방법이 있다. 의존관계가 실행중에 동적으로 변하는 경우는 거의 없으므로 생성자 주입을 권장한다

#### 2. 자바 코드로 직접 스프링빈 등록하기
- 회원 서비스와 회원 리포지토리의 @Service, @Repository, @Autowired 애노테이션을 제거해야한다.
```java
@Configuration
public class SpringConfig { 
	@Bean
 	public MemberService memberService() {
 		return new MemberService(memberRepository());
 	}
 	@Bean
 	public MemberRepository memberRepository() {
		return new MemoryMemberRepository();
 	}
}
```

