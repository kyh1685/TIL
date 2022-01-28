# 객체 지향 원리 적용

_김영한 강사님의 스프링 핵심 원리 - 기본편 강의를 정리한 내용입니다._

![](https://images.velog.io/images/3hee_11/post/a2d0af79-d7e0-47d8-a1da-b5aecb602363/image.png)

_~ 가상의 기획자가 VIP에게 무조건 1000원 할인해주던 정책을 주문금액에 따라 변동해야 한다고 요구하는 상황 ~_

🔔 **할인 정책 변경**
- OrderServiceImple에 적용되는 할인 정책을 변경하기 위해 구현클래스를 FIxDiscountPolicy에서 RateDiscountPolicy로 변경한다.
![](https://images.velog.io/images/3hee_11/post/0d1fb704-d96f-4675-b743-b69f9f639a5e/image.png)
- 정책을 변경을 위해 OrderServiceImpl의 코드를 직접 변경한다.

🚨 **문제점** 🚨
- 인터페이스에만 의존해야 하는데 구현체에도 의지하게 된다.
✔ **DIP 위반**
- 기능을 확장하거나 변경했을 때 클라이언트 코드에 영향을 준다.
✔ **OCP 위반**

💡 **해결 방안** 💡

- **인터페이스에만 의존하도록** 설계 변경!
![](https://images.velog.io/images/3hee_11/post/ff18c83c-0341-4926-9165-661052550eb3/image.png)
- 위와 같은 상태에서는 구현체가 없어서 예외가 발생하므로 누군가가 DiscountPolicy의 구현 객체를 생성하고 대입해주어야 한다.

### 관심사의 분리
- 애플리케이션의 전체 동작 방식을 구성(config)하기 위해, 구현 객체를 생성하고, 연결하는 책임을 가지는 설정 클래스 AppConfig를 만든다.
![](https://images.velog.io/images/3hee_11/post/e02f4fe1-c520-435b-b214-0b16441fc7b5/image.png)
- memberService() 호출시 MemberService의 생성자를 실행시키고 매개변수로 MemoryMemberRepository의 객체를 생성해 넣어준다.
![](https://images.velog.io/images/3hee_11/post/8b1cbdd5-8e39-4f5d-bc9a-3e4496136353/image.png)
- MemberServiceImpl 클래스에서 생성자를 통해 memberRepository의 값을 주입해준다( ✔ **생성자 주입** )

📌 **결과**
- MemberServiceImpl은 MemoryMemberRepository를 의존하지 않고 MemberRepository **인터페이스만 의존**한다( ✔ **DIP 완성** )
- MemberServiceImpl의 생성자를 통해서 **어떤 구현 객체를 주입할지는 오직 외부( AppConfig )에서 결정**된다.
![](https://images.velog.io/images/3hee_11/post/b132b3d6-a17d-4e3c-8601-07e52f066a45/image.png)
- **관심사의 분리**: 객체를 생성하고 연결하는 역할과 실행하는 역할이 명확히 분리되었다.

### AppConfig 리팩터링
- AppConfig에서 중복을 줄이고 역할에 따른 구현이 잘보이게 변경

![](https://images.velog.io/images/3hee_11/post/15d1556a-987f-44ad-bb6c-d6afdf0ba0b0/image.png)👉 **변경 전**
![](https://images.velog.io/images/3hee_11/post/6422835a-5dfd-458c-8226-e92211c67427/image.png)👉 **변경 후** 
![](https://images.velog.io/images/3hee_11/post/ee412ce6-2df0-4dec-9108-5a59d10926eb/image.png)✔ new MemoryMemberRepository() 중복 제거
✔ MemoryMemberRepository를 다른 구현체로 변경할 때 한 부분만 변경하면 된다.

### IoC, DI

#### 제어의 역전(IoC : Inversion of Control)
- 프로그램의 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것이다.

#### 의존관계 주입(DI : Dependency Injection)
- **정적인 의존관계** 
  - 애플리케이션을 실행하지 않아도 클래스의 import만 보고 의존관계를 파악할 수 있다. 
  - 그런데 이러한 클래스 의존관계 만으로는 실제 어떤 객체가 주입 될지 알 수 없다
![](https://images.velog.io/images/3hee_11/post/cf1f971a-995f-4c95-9dcf-5016b333c410/image.png)
- **동적인 의존관계**
  - 애플리케이션 실행 시점(런타임)에 외부에서 실제 구현 객체를 생성하고 클라이언트에 전달해서 클라이언트와 서버의 실제 의존관계가 연결 되는 것을 의존관계 주입이라 한다.
  ![](https://images.velog.io/images/3hee_11/post/e7d562d2-503a-461d-868b-8e655a23ba5c/image.png)
  - 의존관계 주입을 사용하면 클라이언트 코드를 변경하지 않고, 클라이언트가 호출하는 대상의 타입 인스턴스를 변경할 수 있다.

### 스프링으로 전환

#### AppConfig 변경
![](https://images.velog.io/images/3hee_11/post/a7f79391-89e3-4e27-a3cb-d0ecafb425d3/image.png)
- AppConfig에 설정을 구성한다는 뜻의 @Configration을 붙인다.
- 각 메소드에 @Bean을 붙인다.
👉 스프링 컨테이너에 스프링 빈으로 등록해준다.

#### MemberApp에 스프링 컨테이너 적용![](https://images.velog.io/images/3hee_11/post/638bfa21-b255-4c54-88ce-3db09a4e30ce/image.png)

⭐ **스프링 컨테이너** ⭐
- ApplicationContext를 스프링 컨테이너라 한다.
- 스프링 컨테이너는 **@Configuration이 붙은 AppConfig를 설정 정보로 사용**한다. 여기서 @Bean이라 적힌 메서드를 모두 호출해서 **반환된 객체를 스프링 컨테이너에 등록**한다. 이렇게 **스프링 컨테이너에 등록된 객체를 스프링 빈**이라 한다.
- 스프링 빈은 @Bean 이 붙은 메서드의 명을 스프링 빈의 이름으로 사용한다.

📌 **결과**
기존에는 개발자가 직접 자바코드로 모든 것을 했다면 이제부터는 스프링 컨테이너에 객체를 스프링 빈으로 등록하고, 스프링 컨테이너에서 스프링 빈을 찾아서( getBean() ) 사용하도록 변경되었다.
