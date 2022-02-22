# 도메인 분석 설계

_김영한 강사님의 실전! 스프링 부트와 JPA 활용1 - 웹 애플리케이션 개발 강의를 정리한 내용입니다._

## 요구사항 분석
> **💡 요구사항 💡 **
- 회원 기능(등록, 조회)
- 상품 기능(등록, 수정, 조회)
- 주문 기능(주문, 내역 조회, 주문 취소)
- 기타(상품 재고 관리, 종류, 카테고리 구분, 주문시 배송 정보 입력)

## 도메인 모델과 테이블 설계
![](https://images.velog.io/images/3hee_11/post/c9f7f0ca-1318-4320-b8d8-7a1aa4a72f3c/image.png)

- **회원, 주문, 상품의 관계** : 회원은 여러 상품을 주문할 수 있고 한 번 주문할 때 여러 상품을 선택할 수 있으므로 **주문과 상품은 다대다 관계.** 다대다 관계는 관계형 데이터베이스, 엔티티에서 거의 사용하지 않기 때문에 주문 상품이라는 엔티티를 추가해서 **다대다 관계를 일대다, 다대일 관계로 풀어냄**

- **상품 분류** : 상품은 도서, 음반, 영화로 구분되는데 **상품이라는 공통 속성을 사용하므로 상속 구조로 표현**

### 회원 엔티티 분석
![](https://images.velog.io/images/3hee_11/post/287675f6-700d-4acf-9268-cbfc34738f90/image.png)

**🤚 참고**
- 실무에서는 회원이 주문을 참조하지 않고, 주문이 회원을 참조하는 것으로 충분하다(여기서는 일대다, 다대일의 양방향 연관관계를 설명하기 위해 추가)

### 회원 테이블 분석
![](https://images.velog.io/images/3hee_11/post/415de7db-bdeb-469b-8232-f0a796d203b1/image.png)

- **ITEM** : 앨범, 도서, 영화를 통합해서 테이블로 만들었다(DTYPE 컬럼으로 타입 구분)

**🤚 참고**
- 테이블명이 ORDERS인 이유는 데이터베이스가 order by를 예약어로 잡고 있는 경우가 많기 때문이다.

### 연관관계 매핑 분석
- **회원과 주문** 
: <span style='background-color: #fff5b1'>일대다 , 다대일의 양방향 관계</span>로 연관관계의 주인을 정하는데 **외래 키가 있는 주문을 연관관계의 주인으로 정하는 것**이 좋다.
  👉 Order.member를 ORDERS.MEMBER_ID 외래 키와 매핑
- **주문상품과 주문** 
: <span style='background-color: #fff5b1'>다대일 양방향 관계</span>로 외래 키가 주문상품에 있으므로 주문상품이 연관관계의 주인이다. 
👉 OrderItem.order를 ORDER_ITEM.ORDER_ID 외래 키와 매핑
- **주문상품과 상품** 
: <span style='background-color: #fff5b1'>다대일 단방향 관계</span>
👉 OrderItem.item을 ORDER_ITEM.ITEM_ID 외래 키와 매핑
- **주문과 배송** 
: <span style='background-color: #fff5b1'>일대일 양방향 관계</span>
👉 Order.delivery를 ORDERS.DELIVERY_ID 외래 키와 매핑
- **카테고리와 상품** 
: @ManyToMany를 사용해서 매핑한다(**실무에서 @ManyToMany는 사용하지 말기**)

### 📌 외래 키가 있는 곳을 연관관계의 주인으로 정해라.
**연관관계의 주인은 단순히 외래 키를 누가 관리하냐의 문제이지 비즈니스상 우위에 있다고 주인으로 정하면 안된다. **

_예를 들어서 자동차와 바퀴가 있으면, 일대다 관계에서 항상 다쪽에 외래 키가 있으므로 외래 키가 있는 바퀴를 연관관계의 주인으로 정하면 된다. 만약 자동차를 연관관계의 주인으로 정하면 자동차가 관리하지 않는 바퀴 테이블의 외래 키 값이 업데이트 되므로 관리와 유지보수가 어렵고, 추가적으로 별도의 업데이트 쿼리가 발생하는 성능 문제도 있다. _

## 엔티티 클래스 개발

#### 📌 실무에서는 가급적 Getter는 열어두고 Setter는 꼭 필요한 경우에만 사용하자
> Setter는 호출하면 데이터가 변한다. 대신 변경 지점이 명확하도록 비즈니스 메서드를 별도로 제공하자.

#### 📌 실무에서는 @ManyToMany 를 사용하지 말자
> @ManyToMany는 중간 테이블(CATEGORY_ITEM)에 컬럼을 추가할 수 없고, 세밀하게 쿼리를 실행하기 어렵기 때문에 실무에서 사용하기에는 한계가 있다. 중간 엔티티(CategoryItem)를 만들고 @ManyToOne, @OneToMany로 매핑해서 사용하자. **대다대 매핑을 일대다, 다대일 매핑으로 풀어내서 사용하자.**

#### 📌 값 타입(Address)은 변경 불가능하게 설계해야 한다.
> @Setter를 제거하고, 생성자에서 값을 모두 초기화해서 **변경 불가능한 클래스**를 만들자. JPA 스펙상 **엔티티나 임베디드 타입(@Embeddable)은 자바 기본 생성자를 public 또는 protected로 설정**해야 한다(public보다는 protected로 설정하는 것이 더 안전)
_JPA가 이런 제약을 두는 이유는 JPA 구현 라이브러리가 객체를 생성할 때 리플랙션 같은 기술을 사용할 수있도록 지원해야 하기 때문이다._

## 엔티티 설계시 주의점

### 1. 엔티티에는 가급적 Setter를 사용하지 말자
Setter가 모두 열려있다. 변경 포인트가 너무 많아서, 유지보수가 어렵다. 

### 2. 모든 연관관계는 지연로딩으로 설정하자
- 즉시로딩(EAGER)은 예측이 어렵고, 어떤 SQL이 실행될지 추적하기 어렵다(특히 JPQL을 실행할 때 N+1 문제가 자주 발생)
- **실무에서 모든 연관관계는 지연로딩(LAZY)으로 설정해야 한다.**
- 연관된 엔티티를 함께 DB에서 조회해야 하면 fetch join 또는 엔티티 그래프 기능을 사용한다.
- **@XToOne(OneToOne, ManyToOne) 관계는 기본이 즉시로딩이므로 직접 지연로딩으로 설정**해야 한다.
![](https://images.velog.io/images/3hee_11/post/0c1e2c31-585d-459e-9f13-f39bad44e03a/image.png)

### 3. 컬렉션은 필드에서 초기화 하자
- 컬렉션은 필드에서 바로 초기화 하는 것이 안전하다(null 문제에서 안전)
- **하이버네이트는 엔티티를 영속화 할 때, 컬랙션을 감싸서 하이버네이트가 제공하는 내장 컬렉션으로 변경한다.** 
- 만약 getOrders()처럼 임의의 메서드에서 컬렉션을 잘못 생성하면 하이버네이트 내부 메커니즘에 문제가 발생할 수 있다.
![](https://images.velog.io/images/3hee_11/post/c7a9187f-dfdc-44f2-9811-b63932007aef/image.png)

### 4. 테이블, 컬럼명 생성 전략
- 스프링 부트에서 하이버네이트 기본 매핑 전략을 변경해서 실제 테이블 필드명은 다르다([참고1](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#howtoconfigure-hibernate-naming-strategy), [참고2](http://docs.jboss.org/hibernate/orm/5.4/userguide/html_single/Hibernate_User_Guide.html#naming))
- 하이버네이트 기존 구현 : 엔티티의 필드명을 그대로 테이블의 컬럼명으로 사용(SpringPhysicalNamingStrategy)
- 스프링 부트 신규 설정 (엔티티(필드) 테이블(컬럼))
  - 카멜 케이스 → 언더스코어(memberPoint → member_point)
  - .(점) → _(언더스코어)
  - 대문자 → 소문자
### 5. cascade = CascadeType.ALL
![](https://images.velog.io/images/3hee_11/post/5607cbde-2c3f-4af3-bed3-75eeaae4bffd/image.png)
- 엔티티의 상태 변화를 전파시키는 옵션이다.
- 만약 엔티티의 상태 변화가 있으면 연관되어 있는(ex. @OneToMany, @ManyToOne) 엔티티에도 상태 변화를 전이시키는 옵션이다.

### 6. 연관관계 메서드
![](https://images.velog.io/images/3hee_11/post/92f5faa6-9b69-486d-8172-815c5a535cb7/image.png)
**참고)** [연관관계 편의 메서드](https://joanne.tistory.com/220)

