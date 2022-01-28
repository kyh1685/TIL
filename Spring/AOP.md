# AOP

_김영한 강사님의 스프링 입문 강의를 정리한 내용입니다._

### AOP
- AOP: Aspect Oriented Programming

### AOP가 필요한 상황
- 모든 메소드의 호출 시간을 측정하고 싶을 때
- 공통 관심 사항(cross-cutting concern) vs 핵심 관심 사항(core concern)
- 회원 가입 시간, 회원 조회 시간을 측정하고 싶을 때

### AOP 적용
![](https://images.velog.io/images/3hee_11/post/f3c55c83-07e4-4bc8-8040-33f44145f997/image.png)

#### 시간 측정 AOP 등록
- 회원가입, 회원 조회등 핵심 관심사항과 시간을 측정하는 공통 관심 사항을 분리한다.
- 시간을 측정하는 로직을 별도의 공통 로직으로 만들었다.
- 변경이 필요하면 이 로직만 변경하면 된다.
- 원하는 적용 대상을 선택할 수 있다.

```java
@Component
@Aspect
public class TimeTraceAop {
 	@Around("execution(* hello.hellospring..*(..))")
 	public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
 		long start = System.currentTimeMillis();
 		System.out.println("START: " + joinPoint.toString());
        
 		try {
 			return joinPoint.proceed();
 		} finally {
 			long finish = System.currentTimeMillis();
 			long timeMs = finish - start;
 			System.out.println("END: " + joinPoint.toString()+ " " + timeMs + "ms");
 		}
 	}
}
```
![](https://images.velog.io/images/3hee_11/post/c8e6f40a-5861-4f13-a154-2d62db3799bc/image.png)
