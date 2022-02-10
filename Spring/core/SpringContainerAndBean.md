# 스프링 컨테이너와 스프링 빈

_김영한 강사님의 스프링 핵심 원리 - 기본편 강의를 정리한 내용입니다._

### 스프링 컨테이너 생성 과정

#### 1. 스프링 컨테이너 생성
![](https://images.velog.io/images/3hee_11/post/c8d9a455-3f1e-4cca-94c9-ecd43f46c549/image.png)
- ```java 
new AnnotationConfigApplicationContext(AppConfig.class) ```
- 컨테이너를 생성할 때는 구성 정보를 지정해줘야 한다.
👉 AppConfig를 구성정보로 지정

#### 2. 스프링 빈 등록
![](https://images.velog.io/images/3hee_11/post/04404d25-a1e3-4253-a6e3-cd0bfc60132b/image.png)
- 스프링 컨테이너는 파라미터로 넘어온 설정 클래스 정보를 사용해서 스프링 빈을 등록한다.
- 빈 이름은 메소드 명을 사용하며 직접 부여할 수도 있다. 
```java
@Bean(name="memberService2")
```
🚩 **주의! 빈 이름은 항상 다른 이름으로 부여해야 한다.**

#### 3. 스프링 빈 의존관계 설정 - 준비
![](https://images.velog.io/images/3hee_11/post/1a76eeac-c3d5-4b7f-917f-b6e6e32ff353/image.png)

#### 4. 스프링 빈 의존관계 설정 - 완료
![](https://images.velog.io/images/3hee_11/post/d5a43fdb-b760-4529-8e46-4de6ef5d223e/image.png)
- 스프링 컨테이너는 설정 정보를 참고해서 의존관계를 주입(DI)한다.

### 컨테이너에 등록된 빈 조회
```java
// 스프링에서 기본적으로 사용해서 등록된 빈 제외하고 직접 등록한 빈만 보기
@Test
@DisplayName("애플리케이션 빈 출력하기")
void findApplicationBean(){
    // 컨테이너에서 Bean들 이름 가져오기
    String[] beanDefinitionNames = ac.getBeanDefinitionNames();

    for (String beanDefinitionName : beanDefinitionNames) {
        // 이름으로 빈 정보 찾아서 변수에 대입하기
        BeanDefinition beanDefinition = ac.getBeanDefinition(
                                        beanDefinitionName);

        //Role ROLE_APPLICATION: 직접 등록한 애플리케이션 빈
        //Role ROLE_INFRASTRUCTURE: 스프링이 내부에서 사용하는 빈
        // 빈 정보에서 가져온 Role이 직접 등록한 빈인지 체크
        if(beanDefinition.getRole()== BeanDefinition.ROLE_APPLICATION){
            Object bean = ac.getBean(beanDefinitionName);
            System.out.println("name = " + beanDefinitionName 
                             + " object = " + bean);
		}
	}
}
```
- 이름없이 타입으로만 조회할 수도 있다.
- 클래스 대신 구체 타입(ex.MemberServiceImpl.class)로 조회할 수 있다.
👉 구체 타입 변경시 유연성이 떨어진다.
- 같은 타입이 둘 이상있으면 오류가 발생하므로 이름을 지정해준다
👉    ac.getBean("memberRepository1",MemberRepository.class)

#### 특정 타입 모두 조회하기
```java
@Test
@DisplayName("특정 타입을 모두 조회하기")
void findAllBeanByType() {
	Map<String, MemberRepository> beansOfType =
	ac.getBeansOfType(MemberRepository.class);
    
	for (String key : beansOfType.keySet()) {
		System.out.println("key = " + key + " value = " +
				   beansOfType.get(key));
	}
 
 	System.out.println("beansOfType = " + beansOfType);
 	assertThat(beansOfType.size()).isEqualTo(2);
 }
```

### BeanFactory와 ApplicationContext
<img src="https://images.velog.io/images/3hee_11/post/46bcd968-1aa3-41fa-98d3-c68f097174e9/image.png" width="40%">

#### ✔ BeanFactory
- 스프링 컨테이너의 최상위 인터페이스이다.
- 스프링빈을 관리하고 조회하는 역할을 한다.
- getBean()을 제공한다.

#### ✔ ApplicationContext
- BeanFactory의 모든 기능을 상속받아 제공한다.
- 빈 관리 기능(BeanFactory) + 편리한 부가기능을 제공한다.
- BeanFactory나 ApplicationContext를 스프링 컨테이너라 한다.
![](https://images.velog.io/images/3hee_11/post/8a9ddb9f-4c5c-48c3-9d1e-10b5662f6600/image.png)

**ApplicationContext의 부가기능**

1. **메시지소스를 활용한 국제화 기능**
👉 한국에서 들어오면 한국어로, 영어권에서 들어오면 영어로 출력
2. **환경변수**
👉 로컬, 개발, 운영등을 구분해서 처리
3. **애플리케이션 이벤트**
👉 이벤트를 발행하고 구독하는 모델을 편리하게 지원
4. **편리한 리소스 조회**
👉 파일, 클래스패스, 외부 등에서 리소스를 편리하게 조회

### 설정 형식 - 자바 코드, xml
<img src="https://images.velog.io/images/3hee_11/post/ea7d13e2-56f4-4b57-939c-7dd25c8df501/image.png" width="90%">

#### 어노테이션 기반 자바 코드 설정
- AnnotationConfigApplicationContext 클래스를 사용하면서 자바 코드로 된 설정 정보를 넘기면 된다.
![](https://images.velog.io/images/3hee_11/post/deb6e6a9-d419-4a35-bd04-9c5d25cefcd1/image.png)

#### xml 설정 사용
- GenericXmlApplicationContext 를 사용하면서 xml 설정 파일을 넘기면 된다. 
- xml 파일은 src/main/resources/appConfig.xml에 생성
![](https://images.velog.io/images/3hee_11/post/f997eb48-290a-4220-8f08-6d7362911a87/image.png)![](https://images.velog.io/images/3hee_11/post/79eaddac-5a90-4e7d-a39c-79d7cd6e21a6/image.png)

### 스프링 빈 설정 메타 정보 - BeanDefinition
![](https://images.velog.io/images/3hee_11/post/b76c4cd6-e057-400f-b8a3-9655beff8cdc/image.png)
- 역할과 구현
  - BeanDefinition은 **인터페이스**이다.
  - 스프링 컨테이너는 자바 코드인지 xml인지 몰라도 된다. **인터페이스, 즉 역할만 알면 된다.** 
- BeanDefinition을 빈 설정 메타 정보라고 한다.
  -** @Bean, < bean >당 하나씩 메타 정보가 생성**된다.
- 스프링 컨테이너는 **메타 정보를 기준으로 스프링 빈을 생성**한다.
![](https://images.velog.io/images/3hee_11/post/fb7f4e6f-f095-4593-aa1e-5a9c4293eb2e/image.png)
- AnnotationConfigApplicationContext는 AnnotatedBeanDefinitionReader를 사용해서 AppConfig.class 를 읽고 BeanDefinition을 생성한다.
![](https://images.velog.io/images/3hee_11/post/2d1ebcb2-5a99-4cba-aa22-936c684efb95/image.png)
- 새로운 형식의 설정 정보가 추가되면, XxxBeanDefinitionReader를 만들어서 BeanDefinition을 생성하면 된다.

#### BeanDefinition 정보
- BeanClassName: 생성할 빈의 클래스 명(자바 설정 처럼 팩토리 역할의 빈을 사용하면 없음)
- factoryBeanName: 팩토리 역할의 빈을 사용할 경우 이름 
예) appConfig
- factoryMethodName: 빈을 생성할 팩토리 메서드 지정 
예) memberService
- Scope: 싱글톤(기본값)
- lazyInit: 스프링 컨테이너를 생성할 때 빈을 생성하는 것이 아니라, 실제 빈을 사용할 때 까지 최대한 생성을 지연처리 하는지 여부
- InitMethodName: 빈을 생성하고, 의존관계를 적용한 뒤에 호출되는 초기화 메서드 명
- DestroyMethodName: 빈의 생명주기가 끝나서 제거하기 직전에 호출되는 메서드 명
- Constructor arguments, Properties: 의존관계 주입에서 사용한다. (자바 설정 처럼 팩토리 역할의 빈을 사용하면 없음)
