# 스프링 DB 접근 기술

_김영한 강사님의 스프링 입문 강의를 정리한 내용입니다._

## 순수 JDBC
###  환경설정
- build.gradle 파일에 jdbc, h2 데이터베이스 관련 라이브러리 추가
```
implementation 'org.springframework.boot:spring-boot-starter-jdbc'
runtimeOnly 'com.h2database:h2'
```
- 스프링 부트 데이터베이스 연결 설정 추가 [resources/application.properties
]
```
spring.datasource.url=jdbc:h2:tcp://localhost/~/test
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
```
### Jdbc 리포지토리 구현
#### Jdbc 회원 리포지토리
- sql을 직접 작성하고 Connection을 가져온다. ptsmt에 파라미터를 직접 세팅해주고 실행한 후 ResultSet으로 값을 반환받는다. 
```java
public class JdbcMemberRepository implements MemberRepository {
 	private final DataSource dataSource;
 	
    public JdbcMemberRepository(DataSource dataSource) {
 		this.dataSource = dataSource;
 	}
    
 	@Override
 	public Member save(Member member) {
 		String sql = "insert into member(name) values(?)";
        
 		Connection conn = null;
 		PreparedStatement pstmt = null;
 		ResultSet rs = null; 
        try {
 		conn = getConnection();
 		pstmt = conn.prepareStatement(sql,
		Statement.RETURN_GENERATED_KEYS);
 		pstmt.setString(1, member.getName());
 		pstmt.executeUpdate();
 		rs = pstmt.getGeneratedKeys();
 		if (rs.next()) {
 			member.setId(rs.getLong(1));
 		} else {
 			throw new SQLException("id 조회 실패");
 		}
 		return member;
 } catch (Exception e) {
 	throw new IllegalStateException(e);
 } finally {
 	close(conn, pstmt, rs);
 }
}
```

### 스프링 설정 변경
- DataSource는 데이터베이스 커넥션을 획득할 때 사용하는 객체다.
- 스프링 부트는 데이터베이스 커넥션 정보를 바탕으로 DataSource를 생성하고 스프링 빈으로 만들어두기 때문에 DI를 받을 수 있다.
```java
@Configuration
public class SpringConfig {
 private final DataSource dataSource;
 
 public SpringConfig(DataSource dataSource) {
 this.dataSource = dataSource;
 }
 
 @Bean
 public MemberService memberService() {
 return new MemberService(memberRepository());
 }
 
 @Bean
 public MemberRepository memberRepository() {
// return new MemoryMemberRepository();
return new JdbcMemberRepository(dataSource);
 }
}
```

### 스프링 통합 테스트
- SpringBootTest : 스프링 컨테이너와 테스트를 함께 실행한다.
- Transactional : 테스트 시작 전에 트랜잭션을 시작하고 테스트가 끝나면 롤백 시켜주기 때문에 데이터에 영향을 미치지 않는다.
```java
@SpringBootTest
@Transactional
class MemberServiceIntegrationTest {
 	@Autowired MemberService memberService;
 	@Autowired MemberRepository memberRepository;
 
 	@Test
 	public void 회원가입() throws Exception {
 		//Given
 		Member member = new Member();
 		member.setName("hello");
        
 		//When
 		Long saveId = memberService.join(member); //Then
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
 		() -> memberService.join(member2));//예외가 발생해야 한다.
        
 		assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");
 	}
}
```

## 스프링 JDBCTemplate
- 환경설정은 순수 JDBC와 동일하다.
- 스프링 JdbcTemplate과 MyBatis 같은 라이브러리는 JDBC API에서 본 반복 코드를 대부분 제거해주지만 sql문은 직접 작성해야 한다.

### 스프링 JdbcTemplate 회원 리포지토리
``` java
public class JdbcTemplateMemberRepository implements MemberRepository {
 	private final JdbcTemplate jdbcTemplate;
 	
 	public JdbcTemplateMemberRepository(DataSource dataSource) {
 		jdbcTemplate = new JdbcTemplate(dataSource);
 	}
    
 	@Override
 	public Member save(Member member) {
 		SimpleJdbcInsert jdbcInsert = new SimpleJdbcInsert(jdbcTemplate);
        
 		jdbcInsert.withTableName("member").usingGeneratedKeyColumns("id");
        
 		Map<String, Object> parameters = new HashMap<>();
 		parameters.put("name", member.getName());
        
 		Number key = jdbcInsert.executeAndReturnKey(new MapSqlParameterSource(parameters));
 		member.setId(key.longValue());
        
 		return member;
        }
        
	@Override
	 public Optional<Member> findById(Long id) {
	 	List<Member> result = jdbcTemplate.query("select * from member where id =?", memberRowMapper(), id);
        
	 	return result.stream().findAny();
	 }
}
```

### 스프링 설정 변경
- springConfig에서 memberRepository return값을 아래처럼 바꿔준다.
```java
return new JdbcTemplateMemberRepository(dataSource
```

## JPA
- JPA는 반복 코드도 줄여주고 SQL문도 JPA가 만들어서 실행시켜준다.
- JPA를 사용하면 SQL과 데이터 중심의 설계에서 객체 중심의 설계로 패러다임을 전환을 할 수 있다.
- 개발 생산성을 높일 수 있다.

### build.gradle 파일에 JPA, h2 데이터베이스 관련 라이브러리 추가
- jpa 내부에 jdbc 관련 라이브러리가 포함되므로 기존 jdbc 라이브러리 코드는 주석 제거!
```java
implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
runtimeOnly 'com.h2database:h2'
testImplementation('org.springframework.boot:spring-boot-starter-test') {
exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
}
```

### 스프링 부트에 JPA 설정 추가
- resources/application.properties
```java
spring.jpa.show-sql=true // JPA가 만든 SQL 출력
spring.jpa.hibernate.ddl-auto=none
// JPA는 테이블을 자동으로 생성하는 기능이 있으므로 없애려면 none 없애지 않으려면 create
```

### JPA 엔티티 매핑
```java
@Entity
public class Member {
 	@Id @GeneratedValue(strategy = GenerationType.IDENTITY)
 
 	private Long id;
 	private String name;
 	
    	public Long getId() {
 		return id;
 	}
 	
    	public void setId(Long id) {
 		this.id = id;
 	}
 	
    	public String getName() {
 		return name;
 	}
 	
    	public void setName(String name) {
 		this.name = name;
 	}
}
```

### JPA 회원 리포지토리
```java
public class JpaMemberRepository implements MemberRepository {
 	private final EntityManager em;
    
 	public JpaMemberRepository(EntityManager em) {
 		this.em = em;
 	}
    
 	public Member save(Member member) {
 		em.persist(member);
 		return member;
 	}
    
 	public Optional<Member> findById(Long id) {
 		Member member = em.find(Member.class, id);
 		return Optional.ofNullable(member);
 	}
    
 	public List<Member> findAll() {
 		return em.createQuery("select m from Member m", Member.class)
 		.getResultList();
 	}
    
 	public Optional<Member> findByName(String name) {
 		List<Member> result = em.createQuery("select m from Member m where 
		m.name = :name", Member.class)
 			.setParameter("name", name)
 			.getResultList();
 		return result.stream().findAny();
 	}
}
```

### 서비스 계층에 트랜잭션 추가
- 스프링은 해당 클래스의 메서드를 실행할 때 트랜잭션을 시작하고, 메서드가 정상 종료되면 트랜잭션을 커밋한다. 만약 런타임 예외가 발생하면 롤백한다.
- JPA를 통한 모든 데이터 변경은 트랜잭션 안에서 실행해야 한다.
```java
import org.springframework.transaction.annotation.Transactional

@Transactional
```

### 스프링 설정 변경
- springConfig에서 memberRepository return값을 아래처럼 바꿔준다.
```java
@Configuration
public class SpringConfig {
 	private final DataSource dataSource;
 	private final EntityManager em;
 	
    	public SpringConfig(DataSource dataSource, EntityManager em) {
 		this.dataSource = dataSource;
 		this.em = em;
 	} 
    
    	@Bean
 	public MemberService memberService() {
 		return new MemberService(memberRepository());
 	}
 	
    	@Bean
 	public MemberRepository memberRepository() {
 		return new JpaMemberRepository(em);
 	}
}
```

## 스프링 데이터 JPA
- 스프링 데이터 JPA를 사용하면 리포지토리에 구현 클래스 없이 인터페이스 만으로 개발을 완료할 수 있다. 그리고 반복 개발해온 기본 CRUD 기능도 스프링 데이터
JPA가 모두 제공한다.

### 스프링 데이터 JPA 회원 리포지토리]
- JpaRepository를 상속받으면 스프링 데이터 JPA가 알아서 구현 객체를 생성해서 스프링 빈으로 등록해준다.
```java
public interface SpringDataJpaMemberRepository extends JpaRepository<Member,
Long>, MemberRepository {
 	Optional<Member> findByName(String name);
}
```

### 스프링 데이터 JPA 회원 리포지토리를 사용하도록 스프링 설정 변경

```java
@Configuration
public class SpringConfig {
	// 스프링 데이터 JPA가 생성한 구현 객체가 자동으로 등록됨
 	private final MemberRepository memberRepository;
 
 	public SpringConfig(MemberRepository memberRepository) {
 		this.memberRepository = memberRepository;
 	}
 
 	@Bean
 	public MemberService memberService() {
 		return new MemberService(memberRepository);
 	}
}
```

### 스프링 데이터 JPA 제공 클래스
- 스프링 데이터 JPA 제공 기능		
   - 인터페이스를 통한 기본적인 CRUD
  - findByName() , findByEmail() 처럼 메서드 이름 만으로 조회 기능 제공
  - 페이징 기능 자동 제공
