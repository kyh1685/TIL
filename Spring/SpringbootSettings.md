# Spring Boot 프로젝트 환경 설정

_김영한 강사님의 스프링 입문 강의를 정리한 내용입니다._

스프링부트 스타터 사이트로 들어가서 프로젝트를 생성해준다.

[✔ 스프링부트 스타터](https://start.spring.io/)

![](https://images.velog.io/images/3hee_11/post/4f2cb01b-3377-47da-b9d6-4b7662fc9ad2/image.png)

잘 동작하는지 확인하기 위해 인텔리J에서 메인 클래스를 실행했는데 오류가 났다.

![](https://images.velog.io/images/3hee_11/post/87ebe373-d07a-4ed5-9477-ecaeb5abe0cb/20211028_153048.png)

**원인1 **
Gradle에서 Java 버전이 잘못 설정되어 있음
**해결1**
1. file > settings > build,execution, deployment > build tool > gradle 로 들어가기
2. Build and run using과 Run tests using 을 IntelliJ IDEA로 변경
3. Gradle JVM을 JAVA11 버전으로 변경 후 Apply > Ok를 눌러준다.

❗ Gradle을 통하지 않고 자바로 바로 실행하면 실행속도가 더 빠르므로 Build and run using과 Run tests using 을 IntelliJ IDEA로 추가로 변경해준다. 

위 설정을 해주고 다시 실행시켰는데 이번에는 8080 포트가 이미 사용중이라고 떠서 포트번호를 변경해주었다.

![](https://images.velog.io/images/3hee_11/post/60f4b539-6d6d-4c39-8250-acc07f3f243a/20211028_152154.png)

1. 상단 메뉴 run > Edit Configurations 선택
2. 위 화면에서 Environment Variables 옆에 아이콘을 눌러서 Name:server.port, Value:설정할 번호로 설정하고 Apply > Ok를 눌러준다.

**스프링 동작 환경**
![](https://images.velog.io/images/3hee_11/post/6996e30c-ebd7-428d-b18d-a24abe83f724/20211028_160002.png)
- Controller에서 return값으로 문자열을 리턴하면 viewResolver가 /templates/문자열+.html을 붙여서 화면을 찾는다.

참고 : spring-boot-devtools 라이브러리를 추가하면, html 파일을 컴파일만 해주면 서버 재시작 없이 View 파일 변경이 가능하다. (인텔리J 컴파일 방법: 메뉴 build Recompile)

**콘솔에서 빌드하고 실행하기**
1. ./gradlew build
2. cd build/libs
3. java -jar hello-spring-0.0.1-SNAPSHOT.jar
4. 실행 확인

**IntelliJ에서 단축키 검색하기**
![](https://images.velog.io/images/3hee_11/post/1f674f44-cb1d-475f-98b6-2f95f20a2cf0/20211028_153551.png)
file > setting > keymap 에서 기능 검색하기

**용어**
> - Gradle,Maven : 필요한 라이브러리를 자동으로 가져오고 빌드해주는 라이프 사이클까지 관리해주는 툴(Gradle은 의존관계가 있는 라이브러리까지 함께 다운로드함)
- 테스트 주도 개발(TDD) : 테스트 틀을 먼저 만들고 구현체를 생성한 다음에 틀에 끼워보는 것
