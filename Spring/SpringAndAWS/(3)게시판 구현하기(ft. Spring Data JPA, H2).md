_스프링부트와 AWS로 혼자 구현하는 웹서비스 책을 정리한 내용입니다._

# JPA
<span style='padding: 0px 7px; background-color: #F7DDBE; border-radius: 4px'>자바 표준 ORM(Object Relational Mapping)</span>

> **🤔 MyBatis도 ORM일까?**
- MyBatis는 ORM이 아니라 SQL Mapper이다. 
- ORM은 객체를 매핑하는 것이고, SQL Mapper는 쿼리를 매핑한다.

개발자는 객체지향적인 프로그래밍을 하고,** JPA가 관계형 데이터베이스에 맞게 SQL을 대신 생성해서 실행**한다.
👉 SQL에 종속적인 개발이 아닌 **객체 중심으로 개발**

## Spring Data JPA
- <span style='padding: 0px 7px; background-color: #F7DDBE; border-radius: 4px'>JPA는 인터페이스</span>로서 자바 표준 명세서다. 
- JPA를 구현한 대표적인 구현체로 <span style='padding: 0px 7px; background-color: #F7DDBE; border-radius: 4px'>Hibernate</span>, <span style='padding: 0px 7px; background-color: #F7DDBE; border-radius: 4px'>Eclipse Link</span>등이 있다.
- Spring에서 JPA를 사용할 때, 이 구현체들을 직접 다루진 않는다. 
- 구현체들을 쉽게 이용하기 위해 추상화시킨 <span style='padding: 0px 7px; background-color: #F7DDBE; border-radius: 4px'>Spring Data JPA 모듈</span>을 이용하여 JPA를 다룬다.

>**💡 이들의 관계**
>
<span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>JPA ← Hibernate ← Spring Data JPA</span>

### Spring Data JPA 권장 이유
_Hibernate와 Spring Data JPA를 쓸 때, 둘 사이에 큰 차이는 없음_

- <span style='padding: 0px 7px; background-color: #F7DDBE; border-radius: 4px'>구현체 교체의 용이성</span>
  - Hibernate 외에 다른 구현체로 쉽게 교체하기 위함
  - Spring Data JPA는 내부에서 구현체 매핑을 지원함
- <span style='padding: 0px 7px; background-color: #F7DDBE; border-radius: 4px'>저장소 교체의 용이성</span>
  - RDMS 외에 다른 저장소로 쉽게 교체하기 위함
  - 만약 RDMS에서 MongoDB로 교체한다면 Spring Data JPA에서 Spring Data Mongo로 의존성만 교체하면 됨
👉 Spring Data의 하위 프로젝트는 기본적인 CRUD 인터페이스가 같기 때문!

### JPA와 H2 의존성 등록
```java
// build.gradle
implementation('org.springframework.boot:spring-boot-starter-data-jpa')
implementation('com.h2database:h2')
```

>- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>spring-boot-starter-data-jpa</span> 
  - 스프링부트용 Spring Data JPA 추상화 라이브러리
  - 스프링부트 버전에 맞춰 자동으로 JPA관련 라이브러리 버전 관리
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>h2</span> 
  - 인메모리 관계형 데이터베이스
  - 별도의 설치없이 프로젝트 의존성만으로 관리 가능
  - 메모리에서 실행되기 때문에 애플리케이션 재시작마다 초기화된다는 점을 이용해 테스트 용도로 많이 사용됨
  
### Spring Data JPA 사용
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>domain</span>  패키지 생성(게시글, 댓글, 회원 등 소프트웨어에 대한 요구사항 혹은 문제영역)
- domain 패키지에 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>posts</span> 패키지와 Posts 클래스 생성

>**📌 참고**
어노테이션 순서는 주요 어노테이션을 클래스에 가깝게 둔다.
@Entity는 JPA의 어노테이션이고, @Getter는 롬복의 어노테이션이다.
롬복은 필수가 아니므로 주요 어노테이션인 @Entity를 클래스에 가깝게 둔다.

- Posts 클래스는 DB의 테이블과 매칭될 클래스이며 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>Entity 클래스</span>라고 함

>- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@Entity</span>
  - 테이블과 링크될 클래스임을 나타냄
  - 기본값으로 클래스의 카멜케이스 이름을 언더스코어 네이밍(_)으로 테이블 이름을 매칭
  예) SalesManager.java → sales_manager table
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@Id</span>
  - 해당 테이블의 PK 필드를 나타냄
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@GeneratedValue</span>
  - PK의 생성 규칙을 나타냄
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@Column</span>
  - 테이블의 컬럼을 나타내며 선언하지 않더라도 클래스의 필드는 모두 컬럼이 됨
  - 사용하는 이유는 기본값 외에 추가로 변경이 필요한 옵션이 있을 경우
- - <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@Builder</span>
  - 해당 클래스의 빌더 패턴 클래스 생성
  - 생성자 상단에 선언 시 생성자에 포함된 필드만 빌더에 포함
>
**📌 참고**
Entity의 PK는 Long타입의 Auto_increment  추천
주민번호, 복합키 등은 유니크 키로 별도로 추가하기 

#### 🚨 Entity 클래스에 Setter 만들지 않기
Setter는 인스턴스 값이 언제 어디서 변하는지 코드상으로 명확하게 구분하기 어려워서 나중에 기능을 변경할 때 매우 복잡해짐. 필드의 값 변경이 필요하면 명확히 목적을 나타낼 수 있는 메소드를 추가하기 

```java
// 나쁜 예
public void 주문서비스_취소이벤트(){
	order.setStatus(false);
}

// 좋은 예
public void 주문서비스_취소이벤트(){
	order.cancelOrder();
}
```

#### 🤔 Setter가 없는데 어떻게 값을 채워서 DB에 넣지?
- 기본적으로는 생성자를 통해 최종 값을 채운 후 DB에 삽입
- 값 변경 필요시 위의 예처럼 의도가 명확한 메서드 호출
- 이 책에서는 생성자 대신 **@Builder를 통해 제공되는 빌더 클래스** 사용
  - 생성자나 빌더나 생성시점에 값을 채우는 건 똑같다.
  - 다만 생성자는 new Test(b,a)처럼 인자의 순서를 바꿔도 코드 실행 전까지 문제를 찾을 수 없지만, 빌더는 아래처럼 어떤 필드에 어떤 값을 채워야 하는지 명확히 인지함 
```java
  Test.builder()
  	.a(a)
    .b(b)
    .build();
```

---

- Posts 클래스로 DB에 접근하게 해줄 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>PostsRepository</span> 인터페이스 생성
  - MyBatis에서 Dao라 불리는 DB 계층 접근자
  - JPA에선 Repository라 불리며 인터페이스로 생성함
  - 인터페이스 생성 후 JpaRepository<Entity 클래스, PK 타입>를 상속하면 기본적인 CRUD 메소드가 자동 생성됨
  
> **🚨 Entity 클래스와 기본 Entity Repository는 함께 위치해야함**
나중에 도메인별로 프로젝트를 분리한다면 Entity 클래스와 기본 Entity Repository는 함께 움직여야하므로 도메인 패키지에서 함께 관리한다.

### Spring Data JPA 테스트 코드 작성
- test 디렉토리에 domain.posts 패키지를 만들고 PostsRepositoryTest 클래스 생성
- save, findAll 기능 테스트
- 별다른 설정없이 @SpringBootTest를 사용하면 H2 DB가 자동으로 실행됨

>- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@After</span> 
  - Junit에서 단위 테스트가 끝날 때마다 수행되는 메소드를 지정
  - 보통은 배포 전 전체 테스트를 수행할 때 테스트간 데이터 침범을 막기 위해 사용
  - 여러 테스트가 동시에 수행되면 테스트용 DB인 H2에 데이터가 남아있어 다음 테스트를 실패할 수 있음
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>postsRepository.save</span>
  - 테이블 posts에 insert/update 쿼리를 실행
  - id 값이 있다면 update, 없다면 insert
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>postsRepository.findAll</span>
  - 테이블 post에 있는 모든 데이터를 조회
  
#### 실행되는 쿼리 보기
<span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>src/main/resources</span>에 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>application.properties</span> 파일 생성 후 아래 코드 추가
```java
spring.jpa.show_sql=true
// 출력되는 쿼리 로그를 MySQL 버전으로 변경
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
```

# 등록/수정/조회 API
API를 만들기 위해 3개의 클래스가 필요하다.
- Request 데이터를 받을 Dto
- API 요청을 받을 Controller
- 트랜잭션, 도메인 기능 간의 순서를 보장하는 Service

> 📌 비즈니스 로직은 Service가 아니라 domain에서 처리한다.

- PostsController를 web 패키지, PostsSaveRequestDto를 web.dto 패키지, PostsService를 service 패키지에 생성

### @Autowired가 없는 이유
롬복이 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@RequiredArgsConstructor</span>로 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>final</span>이 붙은 필드를 포함해 생성자를 만들어줬기 때문이다. 
👉 생성자 주입(생성자가 하나면 @Autowire를 붙이지 않아도 자동으로 주입해줌)

### Entity클래스 Request/Response로 사용 🙅‍♀️
- Entity 클래스는 DB와 연결되는 핵심 클래스다.
- Entity 클래스 기준으로 테이블이 생성되고 스키마가 변경된다.
- 화면 변경은 사소한 것인데 이를 위해 Entity 클래스가 변경된다면 너무 큰 변경이 된다.

👉 View를 위한 클래스인 Dto를 사용하자

### Update할 때 쿼리 수행이 없는 이유
<span style='padding: 0px 7px; background-color: #F7DDBE; border-radius: 4px'>JPA의 영속성 컨텍스트 때문이다.</span>

- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>영속성 컨텍스트란 엔티티를 영구 저장하는 환경</span>
- JPA의 핵심 내용 : 엔티티가 영속성 컨텍스트에 포함되어 있는지, 아닌지
- JPA의 엔티티 매니저가 활성화된 상태(Spring Data JPA를 쓰면 기본 옵션)로 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>트랜잭션 안에서 DB에서 데이터를 가져오면</span> 이 데이터는 영속성 컨텍스트가 유지된 상태이다.
- 이 상태에서 해당 데이터의 값을 변경하면 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>트랜잭션이 끝나는 시점에 변경을 반영</span>한다.

**즉, 트랜잭션 안에서 Entity 객체의 값을 변경하면 Update 쿼리 날릴 필요없음**
👉 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>[**더티 체킹**](https://velog.io/@3hee_11/SPRING-BOOTJPA-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B6%80%ED%8A%B8%EC%99%80-JPA-%ED%99%9C%EC%9A%A91-%EC%9B%B9-%EA%B3%84%EC%B8%B5-%EA%B0%9C%EB%B0%9C)</span>

## 수정/조회 기능 구현
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>PostsApiController</span>
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>PostsService</span>
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>PostsSaveRequestDto</span>
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>PostsResponseDto</span>
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>PostsUpdateRequestDto</span>
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>Posts</span>
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>PostsService</span>

## H2로 확인
- application.properties에 아래 옵션을 추가해 웹 콘솔 옵션을 활성화
```java
spring.h2.console.enabled=true
```

- main 메소드를 실행하고 http://localhost:8080/h2-console 접속
![](https://images.velog.io/images/3hee_11/post/3c4079e1-6359-4709-8f80-93f188ec2fc1/image.png)
- JDBC URL을 jdbc:h2:mem:testdb로 작성하고 Connect 클릭
- POSTS 테이블이 있는지 확인하고 insert 쿼리 실행
```sql
insert into posts (author, content, title) values ('author', 'content', 'title')
```
- 브라우저에서 http://localhost:8080/api/v1/posts/1 접속하여 API 조회 테스트

# JPA Auditing

## 생성시간/수정시간 자동화
- domain/posts 안에 BaseTimeEntity 클래스 생성
- BaseTimeEntity 클래스는 모든 Entity의 상위 클래스가 되어 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>Entity들의 createdDate, modifiedDate를 자동으로 관리하는 역할</span>

>- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@MappedSuperclass</span> : JPA Entity 클래스들이 BaseTimeEntity를 상속할 경우 필드들(createdDate, modifiedDate)도 컬럼으로 인식한도록 한다.
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@EntityListeners(AuditingEntityListener.class)</span> : BaseTimeEntity 클래스에 Auditing 기능을 포함시킨다.
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@createdDate</span> : Entity가 생성되어 저장될 때 시간이 자동 저장된다.
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>@modifiedDate</span> : 조회한 값이 Entity의 값을 변경할 때 시간이 자동 저장된다.

- Posts 클래스가 BaseTimeEntity 클래스를 상속받게 한다.
- Application 클래스에 @EnableJpaAuditing 어노테이션을 추가한다.
