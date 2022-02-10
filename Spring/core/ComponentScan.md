# 컴포넌트 스캔

_김영한 강사님의 스프링 핵심 원리 - 기본편 강의를 정리한 내용입니다._

### 컴포넌트 스캔과 의존관계 주입

**컴포넌트 스캔**
설정 정보가 없어도 @Component 어노테이션이 붙은 클래스들을 스캔해서 자동으로 스프링 빈에 등록한다.

**Autowired**
의존관계를 자동으로 주한다.

```java
@Configuration
@ComponentScan(
        // Filter를 이용해서 제외할 클래스를 설정한다.
        // @Configuration도 안에 들어가보면 @ComponentScan을 가지고 있기 때문에 스캔 대상!
        // 설정이 중복되면 안되니까 만들어놓은 @Configuration을 제외하고 스캔하기위해 작성
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION
        				     , classes = Configuration.class)
)
public class AutoAppConfig {}
```
- 컴포넌트 스캔을 사용하려면 @ComponentScan을 설정 정보에 적용한다.
- 컴포넌트 스캔의 대상이 될 클래스들에 @Component 어노테이션을 적용한다.

#### 컴포너틑 스캔과 자동 의존관계 주입 동작 원리
** @ComponentScan**
![](https://images.velog.io/images/3hee_11/post/58d12923-b715-4bd7-8079-9b6161f08a69/image.png)
- @ComponentScan은 @Component가 붙은 모든 클래스들을 스프링 빈으로 등록한다.

**Autowired 의존관계 자동 주입**
![](https://images.velog.io/images/3hee_11/post/a2335aba-9444-476f-a714-3f8680974b43/image.png)
- 생성자에 @Autowired를 적용하면 스프링 컨테이너가 해당 스프링 빈을 찾아서 주입한다(기본적으로 타입이 같은 빈을 찾아서 주입한다.)

### 탐색 위치와 기본 스캔 대상
```java
@ComponentScan(
	basePackages = "hello.core"  // 하나 지정
    			or
	basePackages = {"hello.core", "hello.service"} // 여러개 지정
}
```
- **basePackages** : 탐색할 패키지의 시작 위치를 지정한다.
- **basePackageClasses** : 지정한 클래스의 패키지를 탐색 시작 위치로 지정한다.
- 만약 **지정하지 않으면 @ComponentScan 이 붙은 설정 정보 클래스의 패키지가 시작 위치**가 된다.
👉 스프링 부트 기본 제공!
👉 스프링부트의 시작 정보인 @SpringBootApplication 내부에도 @ComponentScan이 있다.

**컴포넌트 스캔 기본 대상**
- @Component : 컴포넌트 스캔에서 사용
- @Controlller : 스프링 MVC 컨트롤러로 인식
- @Service : 스프링 비즈니스 로직에서 사용
- @Repository
  - 스프링 데이터 접근 계층으로 인식
  - 데이터 계층의 예외를 스프링 예외로 변환
- @Configuration
  - 스프링 설정 정보로 인식
  - 스프링 빈이 싱글톤을 유지하도록 추가 처리

👉 공통적으로 소스 코드에 @Component를 포함하고 있다.
👉 어노테이션에는 상속관계라는 것이 없다. 어노테이션이 특정 어노테이션을 인식할 수 있는 것은 스프링이 지원하는 기능이다.

_ ~ 참고 ~ useDefaultFilters 옵션은 기본으로 켜져있는데, 이 옵션을 끄면 기본 스캔 대상들이 제외된다._

### 필터
- includeFilters : 컴포넌트 스캔 대상을 추가로 지정한다.
- excludeFilters : 컴포넌트 스캔에서 제외할 대상을 지정한다.
- FilterType 옵션
  - ANNOTATION: 기본값, 애노테이션을 인식해서 동작
  - ASSIGNABLE_TYPE: 지정한 타입과 자식 타입을 인식해서 동작
  - ASPECTJ: AspectJ 패턴 사용
  - REGEX: 정규 표현식
  - CUSTOM: TypeFilter 이라는 인터페이스를 구현해서 처리
- 컴포넌트 스캔에 추가 또는 제외할 클래스, 어노테이션 등에 적어준 후 설정 클래스에서 사용한다.

```java
@Configuration
@ComponentScan(
    includeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),
    excludeFilters = {
    		       @Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class),
                       @Filter(type = FilterType.ASSIGNABLE_TYPE, classes = BeanA.class)
                      }
 )
 ```
 
### 중복 등록과 충돌
- 컴포넌트 스캔으로 인해 자동으로 등록되는 빈 끼리의 충돌
👉 ConflictingBeanDefinitionException 예외 발생
- 수동으로 등록한 빈과 자동으로 등록한 빈의 충돌
<img src="https://images.velog.io/images/3hee_11/post/604f00e7-b954-4ceb-97ef-7dcf5670cbe2/image.png" width=70%>
<img src="https://images.velog.io/images/3hee_11/post/daecf587-56fe-4651-8280-88f20bafd644/image.png" width=70%>
👉 수동 빈이 자동 빈을 오버라이딩
👉 스프링 부트에서는 오류가 발생하도록 기본값을 바꿈
