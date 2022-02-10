# 회원 관리 예제 - 백엔드 개발

_김영한 강사님의 스프링 입문 강의를 정리한 내용입니다._

#### 비즈니스 요구사항 정리
-데이터: 회원ID, 이름
-기능: 회원 등록, 조회
-아직 데이터 저장소가 선정되지 않음(가상의 시나리오)

#### 일반적인 웹 애플리케이션 계층 구조
![](https://images.velog.io/images/3hee_11/post/b1006930-efd6-43f6-982f-71aa90ca3662/image.png)-컨트롤러: 웹 MVC의 컨트롤러 역할
-서비스: 핵심 비즈니스 로직 구현
-리포지토리: 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리
-도메인: 비즈니스 도메인 객체, 예) 회원, 주문, 쿠폰 등등 주로 데이터베이스에 저장하고 관리됨

#### 클래스 의존관계
![](https://images.velog.io/images/3hee_11/post/6fc77206-cc36-4f7d-939b-33d25e7c76c4/image.png)-데이터 저장소가 선정되지 않아서 우선 인터페이스로 구현 클래스를 변경할 수 있도록 설계
-개발을 진행하기 위해서 초기 개발 단계에서는 구현체로 가벼운 메모리 기반의 데이터 저장소 사용

#### 회원 도메인과 리포지토리 만들기
-**객체** : Member Class를 생성해서 id(long), name(String) 필드와 getter, setter 생성
-회원 리파지토리 인터페이스 : 회원가입, 아이디로 찾기, 이름으로 찾기, 회원 전체 조회 기능 생성 
```java
public interface MemberRepository {
	Member save(Member member); 
	Optional<Member> findById(Long id);
	Optional<Member> findByName(String name);
	List<Member> findAll();
}
```
-**회원 리파지토리 메모리 구현체** : 
```java
public class MemoryMemberRepository implements MemberRepository {
 	private static Map<Long, Member> store = new HashMap<>();
 	private static long sequence = 0L;
 	
   	@Override
 	public Member save(Member member) {
 		member.setId(++sequence);
 		store.put(member.getId(), member);
 		return member;
 	}
 	@Override
 	public Optional<Member> findById(Long id) {
 		return Optional.ofNullable(store.get(id));
 	}
 	@Override
 	public List<Member> findAll() {
 		return new ArrayList<>(store.values()); 
        }
 	@Override
 	public Optional<Member> findByName(String name) {
 		return store.values().stream()
 			.filter(member -> member.getName().equals(name))
 			.findAny();
 	}
 	public void clearStore() {
 		store.clear();
 	}
}
```
#### 회원 리포지토리 테스트 케이스 작성
: 개발한 기능을 테스트 할 때 main()이나 웹 어플리케이션의 컨트롤러로 실행하면 실행하는데 오래 걸리고 반복 실행이 어려우며 여러 테스트를 한번에 실행하기 어렵다. 자바의 JUnit이라는 프레임워크로 테스트를 해서 이러한 문제를 해결한다.

**회원 리포지토리 메모리 구현체 테스트**
src/test/java 하위 폴더에 생성한다.
```java
class MemoryMemberRepositoryTest {
 	MemoryMemberRepository repository = new MemoryMemberRepository();
 	
    @AfterEach
 	public void afterEach() {
 		repository.clearStore();
 	}
 	@Test
 	public void save() {
 		//given
 		Member member = new Member();
 		member.setName("spring");
 		//when
 		repository.save(member);
 		//then
 		Member result = repository.findById(member.getId()).get();
 		assertThat(result).isEqualTo(member);
 	}
 	@Test
 	public void findByName() {
 		//given
 		Member member1 = new Member();
 		member1.setName("spring1");
 		repository.save(member1);
 		Member member2 = new Member();
 		member2.setName("spring2");
 		repository.save(member2);
 		//when
 		Member result = repository.findByName("spring1").get();
 		//then assertThat(result).isEqualTo(member1);
 	}
 	@Test
 	public void findAll() {
 		//given
 		Member member1 = new Member();
 		member1.setName("spring1");
 		repository.save(member1);
 		Member member2 = new Member();
 		member2.setName("spring2");
 		repository.save(member2);
 		//when
 		List<Member> result = repository.findAll();
 		//then
 		assertThat(result.size()).isEqualTo(2);
 	}
}
```
- @AfterEach : 한번에 여러 테스트를 실행하면 메모리 DB에 직전 테스트의 결과가 남을 수 있다. 이렇게
되면 다음 이전 테스트 때문에 다음 테스트가 실패할 가능성이 있다. @AfterEach 를 사용하면 각 테스트가
종료될 때 마다 이 기능을 실행한다. 여기서는 메모리 DB에 저장된 데이터를 삭제한다.
- 테스트는 각각 독립적으로 실행되어야 한다. 테스트 순서에 의존관계가 있는 것은 좋은 테스트가 아니다.

#### 회원 서비스 개발
```java
public class MemberService {
 	//private final MemberRepository memberRepository = new MemoryMemberRepository();
    
    	// 서비스가 리파지토리 직접 생성하게 하지말고 의존성 주입(DI) 
    	private final MemberRepository memberRepository;
 
 	public MemberService(MemberRepository memberRepository) {
 		this.memberRepository = memberRepository;
 	}
    
 	/**
 	* 회원가입
 	*/
 	public Long join(Member member) {
 		validateDuplicateMember(member); //중복 회원 검증
 		memberRepository.save(member);
 		return member.getId();
 	}
    
 	private void validateDuplicateMember(Member member) {
 		memberRepository.findByName(member.getName())
 						.ifPresent(m -> {throw new IllegalStateException("이미 존재하는 회원입니다.");});
 	}
    
 	/**
 	* 전체 회원 조회
 	*/
 	public List<Member> findMembers() {
 		return memberRepository.findAll();
 	}
    
 	public Optional<Member> findOne(Long memberId) {
 		return memberRepository.findById(memberId);
 	}
}
```

#### 회원 서비스 테스트
```java
class MemberServiceTest { 
	MemberService memberService;
 	MemoryMemberRepository memberRepository;
 
 	@BeforeEach
 	public void beforeEach() {
 		memberRepository = new MemoryMemberRepository();
 		memberService = new MemberService(memberRepository);
 	}
    
 	@AfterEach
 	public void afterEach() {
 		memberRepository.clearStore();
 	}
    
 	@Test
 	public void 회원가입() throws Exception {
 		//Given
 		Member member = new Member();
 		member.setName("hello");
        
 		//When
 		Long saveId = memberService.join(member);
        
 		//Then
 		Member findMember = memberRepository.findById(saveId).get();
 		assertEquals(member.getName(), findMember.getName());
 	}
    
 	@Test
 	public void 중복_회원_예외() throws Exception {
 		//Given
 		Member member1 = new Member();
 		member1.setName("spring");
 		Member member2 = new Member();
 		member2.setName("spring"); 
        
        	//When
 		memberService.join(member1);
 		IllegalStateException e = assertThrows(IllegalStateException.class,
 	() -> memberService.join(member2)); //예외가 발생해야 한다.
 		
        	assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");
 	}
}
```
- @BeforeEach : 각 테스트 실행 전에 호출된다. 테스트가 서로 영향이 없도록 항상 새로운 객체를 생성하고, 의존관계도 새로 맺어준다.
