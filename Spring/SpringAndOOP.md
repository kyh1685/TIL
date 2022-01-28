# 객체 지향 설계와 스프링

_김영한 강사님의 스프링 핵심 원리 - 기본편 강의를 정리한 내용입니다. _

## 스프링

### 스프링 프레임워크
- 핵심 기술: 스프링 DI 컨테이너, AOP, 이벤트, 기타
- 웹 기술: 스프링 MVC, 스프링 WebFlux
- 데이터 접근 기술: 트랜잭션, JDBC, ORM 지원, XML 지원
- 기술 통합: 캐시, 이메일, 원격접근, 스케줄링
- 테스트: 스프링 기반 테스트 지원
- 언어: 코틀린, 그루비

### 스프링 부트
- **스프링을 편리하게 사용할 수 있도록 지원**한다.
- 단독으로 실행할 수 있는 애플리케이션을 쉽게 생성한다.
- 톰캣같은 **웹 서버를 내장**하고 있기 때문에 별도로 서버를 설치하지 않아도 된다.
- 쉬운 빌드 구성을 위한 **starter 종속성을 제공**한다. 
    - stater 종속성을 가져오면 관련 종속성을 자동으로 끌고와 주기 때문에 설정이 편리하다.
- 스프링과 3rd parth(외부) 라이브러리를 자동으로 구성한다.
    - 스프링 **버전과 호환이 되는 라이브러리를 다운**받도록 해준다(버전 고민X).
- 메트릭, 상태 확인, 외부 구성 같은 프로덕션 준비 기능을 제공한다.
    - 운영 환경에서 모니터링같은 기능을 기본적으로 제공한다.
- 관례에 의한 것들은 기본으로 설정이 되어있고 그 외에는 메뉴얼에 나와있다.

### 스프링의 핵심
- 스프링은 **자바 기반 프레임워크**며 자바는 **객체 지향 언어**이다.
- 스프링은 객체 지향 언어의 특징 살리는 프레임워크이며 **좋은 객체 지향 프로그램을 개발할 수 있도록** 도와준다.

### 객체 지향 프로그래밍
- 객체를 기계의 **부품처럼 조립하여 하나의 프로그램을 완성**하는 것이다.
- 각각의 객체는 메세지와 데이터를 주고 받는다.
- 객체 지향 프로그래밍은 프로그램을 유연하고 **변경이 용이**하게 만든다.
    - 유연과 변경의 용이란? 기계 부품을 변경하듯이 개발하는 방법

#### 객체 지향의 특징
- 추상화
- 캡슐화
- 상속
- **다형성**

### 다형성
- 다형성은 세상을 역할과 구현으로 구분한다. ex) 운전자 - 자동차
- 역할 : **인터페이스**, 구현 : 인터페이스를 구현한 클래스, **구현 객체**
- 역할과 구현으로 구분하면 세상이 단순해지고, 유연해지며 **변경도 편리**해진다.
- **클라이언트는 구현 대상의 내부 구조나 구현 대상 자체가 변경되어도 영향을 받지 않는다.**

### 스프링과 객체 지향
- 스프링은 다형성을 극대화해서 사용할 수 있게 도와준다.
- 스프링의 제어의 역전(IoC), 의존관계 주입(DI)은 다형성을 활용해서 역할과 구현을 편리하게 다룰 수 있도록 지원한다.

### 좋은 객체 지향 설계의 5가지 원칙(SOLID)
- 클린코드로 유명한 로버트 마틴이 좋은 객체 지향 설계의 5가지 원칙을 정리
- SRP: 단일 책임 원칙(single responsibility principle)
    - 한 클래스는 하나의 책임만 가져야 한다.
    - 중요한 기준은 변경이다. **변경이 있을 때 파급 효과가 적으면 단일 책임 원칙을 잘 따른 것**이다.
- OCP: 개방-폐쇄 원칙 (Open/closed principle)
    - 소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야 한다.
    - 한계 ! 다형성을 활용해도 구현 객체를 변경하려면 클라이언트 코드를 변경해야 한다. 
- LSP: 리스코프 치환 원칙 (Liskov substitution principle)
    - 프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다. 
    - ex) 자동차 인터페이스의 엑셀은 앞으로 가는 기능이다. 뒤로 가도록 구현하면 LSP 위반.
- ISP: 인터페이스 분리 원칙 (Interface segregation principle)
    - 특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다.
    - 기능에 따라 인터페이스를 쪼개면 인터페이스가 명확해지고, 대체 가능성이 높아진다.
- DIP: 의존관계 역전 원칙 (Dependency inversion principle)
    - 구체화(구현 클래스)에 의존하지 말고 추상화(인터페이스)에 의존해야 한다.
    
## 정리
- 객체 지향의 핵심은 다형성이나 **다형성 만으로는 부품을 갈아 끼우듯이 개발할 수 없다**(구현 객체를 변경할 때 클라이언트 코드도 함께 변경된다 -> OCP, DIP를 지킬 수 없다)
- 스프링은 다음 기술로 다형성 + OCP, DIP를 가능하게 지원한다.
   - DI(Dependency Injection): 의존관계, 의존성 주입
   - DI 컨테이너 제공