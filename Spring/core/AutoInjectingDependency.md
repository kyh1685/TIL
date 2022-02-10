# 의존관계 자동 주입

_김영한 강사님의 스프링 핵심 원리 - 기본편 강의를 정리한 내용입니다._

### 다양한 의존관계 주입 방법

#### ✔ 생성자 주입
- 생성자를 통해 의존관계를 주입받는 방법이다.
- **생성자 호출 시점에 딱 1번만 호출**된다.
- **불변하고 필수적인 의존관계**에 사용한다.
```java
@Autowired // 생성자 1개면 생략 가능!
public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
	this.memberRepository = memberRepository;
	this.discountPolicy = discountPolicy;
 }
```
![](https://images.velog.io/images/3hee_11/post/668734d0-bb06-459d-84ca-b2659a18d52c/image.png)
🚨 **생성자가 한개라면 @Autowired를 생략해도 자동 주입된다** 🚨

#### ✔ 수정자 주입(setter 주입)
- 변경 가능성이 있는 의존관계에 사용한다.

#### ✔ 필드 주입
- 코드가 간결하지만 외부에서 변경이 불가능해 테스트를 하기 힘들다.
- DI 프레임워크가 없으면 아무것도 할 수 없다.

#### ✔ 일반 메소드 주입
- 한번에 여러 필드를 주입 받을 수 있다.

### 옵션 처리
- @Autowired을 사용하면 required 기본값이 true이기 때문에 자동 주입 대상이 없으면 오류가 난다.
  - required = false : 자동 주입할 대상이 없으면 수정자 메서드 자체가 호출 안된다.
  - @Nullable : 자동 주입할 대상이 없으면 null이 입력된다.
  - Optional<> : 자동 주입할 대상이 없으면 Optional.empty 가 입력된다.

#### 생성자 주입을 쓰는 이유
- 객체를 생성할 때 1번만 호출되기 때문에 **불변하게 설계**할 수 있다.
- 생성자 주입을 사용하면 **데이터를 누락했을 때 컴파일 오류가 발생**한다.
- **final 키워드를 사용**할 수 있기 때문에 생성자에 값이 설정되지 않았다면 컴파일 오류로 알 수 있다.

### 롬복(Lombok)
롬복 라이브러리가 제공하는 @RequiredArgsConstructor 기능을 사용하면 final이 붙은 필드들로 생성자를 자동으로 만들어준다.

#### 롬복 적용
1. build.gradle에 아래 코드를 추가한다.
```java
//lombok 설정 추가
configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}

//dependencies 안에 lombok 라이브러리 추가 
compileOnly 'org.projectlombok:lombok'
annotationProcessor 'org.projectlombok:lombok'
testCompileOnly 'org.projectlombok:lombok'
testAnnotationProcessor 'org.projectlombok:lombok'

```
2. Preferences -> Annotation Processors -> Enable annotation processing 체크
![](https://images.velog.io/images/3hee_11/post/43c23173-bb7d-4885-aab1-2078a465b721/image.png)

### @Autowired 필드 명, @Qualifier, @Primary

🚨 **만약 조회된 빈이 두개 이상이라면?**
👉 @Autowired 필드 명, @Qualifier, @Primary 사용

#### @Autowired 필드 명 매칭
@Autowired 는 타입 매칭을 시도하고 만약 **여러개의 빈이 있으면** 필드 이름 혹은 파라미터 이름으로 빈 이름을 **추가 매칭**한다.
```java
@Autowired
private DiscountPolicy rateDiscountPolicy
```

#### @Qualifier 사용
@Qualifier는 **추가 구분자를 붙여주는 방법**으로 빈의 이름 자체를 변경하는 건 아니다.
```java
// 빈 등록 시 @Qualifier를 붙인다.
@Qualifier("mainDiscountPolicy")

// 주입 시에 @Qualifier를 붙여주고 등록한 이름을 적어준다.
@Autowired
public OrderServiceImpl(MemberRepository memberRepository, @Qualifier("mainDiscountPolicy") DiscountPolicy 
discountPolicy) {
	this.memberRepository = memberRepository;
	this.discountPolicy = discountPolicy;
}
```
- @Qualifier("mainDiscountPolicy")를 못찾으면 mainDiscountPolicy 이름을 가진 스프링빈을 조회한다.
- 컴포넌트 외에 빈 등록 시에도 @Qualifier를 동일하게 사용할 수 있다.

#### @Primary 사용
@Primary는 우선순위를 정하는 방법이다. @Autowired 시에 **여러 빈이 매칭되면 Primary가 우선권**을 가진다.

📌 @Primary와 @Qualifier 둘 중 @Qualifier가 우선순위가 더 높다.

### 어노테이션 직접 만들기
@Qualifier("mainDiscountPolicy")를 쓰면 소괄호에서 글자를 틀려도 컴파일러에서 체크해주지 않아 에러가 발생할 수 있으므로 어노테이션을 직접 만들어 쓰기도 한다.
```java
@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER,
ElementType.TYPE, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented@Qualifier("mainDiscountPolicy")
public @interface MainDiscountPolicy {
}

------------------------------------------------------------

@Component
@MainDiscountPolicy
public class RateDiscountPolicy implements DiscountPolicy {}

------------------------------------------------------------

//생성자 자동 주입
@Autowired
public OrderServiceImpl(MemberRepository memberRepository,
 @MainDiscountPolicy DiscountPolicy discountPolicy) {
	this.memberRepository = memberRepository;
	this.discountPolicy = discountPolicy;
}
```

### List, Map
조회한 빈이 모두 필요할 경우

```java
@Test
    void findAllBean(){
        ApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class, DiscountService.class);

        DiscountService discountService = ac.getBean(DiscountService.class);
        Member member = new Member(1L, "userA", Grade.VIP);
        int discountPrice = discountService.discount(member, 10000, "fixDiscountPolicy")
    }

    static class DiscountService{
        private final Map<String, DiscountPolicy> policyMap;
        private final List<DiscountPolicy> policies;

        @Autowired
        public DiscountService(Map<String, DiscountPolicy> policyMap, List<DiscountPolicy> policies) {
            this.policyMap = policyMap;
            this.policies = policies;
        }

        public int discount(Member member, int price, String discountCode) {
            DiscountPolicy discountPolicy = policyMap.get(discountCode);
            return discountPolicy.discount(member, price);
        }
    }
```
**로직 분석**
- DiscountService는 스프링 컨테이너에 등록되면서 Map으로 모든 DiscountPolicy를 주입받는다.
- discount() 메소드는 discountCode로 "fixDiscountPolicy"가 넘어오면 map에서 fixDiscountPolicy 스프링 빈을 찾아서 실행한다.

**주입 분석**
- Map<String, DiscountPolicy> : map의 키에 스프링 빈의 이름을 넣어주고, 그 값으로
DiscountPolicy 타입으로 조회한 모든 스프링 빈을 담아준다.
- List< DiscountPolicy> : DiscountPolicy 타입으로 조회한 모든 스프링 빈을 담아준다.
만약 해당하는 타입의 스프링 빈이 없으면, 빈 컬렉션이나 Map을 주입한다.

📌 **스프링 컨테이너는 생성자에 클래스 정보를 받는다. 여기에 클래스 정보를 넘기면 해당 클래스가 스프링 빈으로 자동 등록된다.**
```java
new AnnotationConfigApplicationContext(AutoAppConfig.class,DiscountService.class);
```

💡 **수동 빈 등록은 언제 사용하는게 좋을까?**
🚩 어플리케이션에 전체적으로 영향을 미치는 **기술 지원 객체**(데이터 베이스 연결, 공통 로그 처리 등)는 **수동으로 빈을 등록**해서 설정 정보에 나타나게 하는게 유지보수에 좋다.
