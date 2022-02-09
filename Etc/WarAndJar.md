### 🤔 War와 Jar 프로젝트 배포할 때 사용은 해봤는데 정확히 무엇일까?
Jar, War는 Java의 jar 툴을 이용하여 생성된 압축 파일이며 어플리케이션을 쉽게 배포하고 동작시킬 수 있도록 있도록 관련 파일들을 패키징해준다.

#### Jar (Java Archive)
- 확장자 파일은 Class와 같은 Java 리소스와 속성 파일,  라이브러리 및 액세서리 파일을 포함한다.
- **원하는 구조로 구성이 가능**하며 JDK에 포함하고 있는 **JRE만 있어도 실행이 가능**하다.

#### War (Web Application Archive)
- servlet, jsp 컨테이너에 배치 할 수 있는 웹 어플리케이션 압축 파일 포맷이다.
- jsp,  servlet, jar, class, xml, html, javascript 등  servlet context 관련 파일들로 패키징 되어있다.
- **사전 정의 된 구조**(WEB-INF 및 META-INF 디렉토리)를 사용하며 실행하려면 Tomcat 등의 웹 서버 또는 웹 컨테이너가 필요하다(web.xml에서 path 설정을 해줘야 WAS가 이것을 읽고 실행)
  
#### 📌 War와 Jar의 차이점 정리!
Jar는 JRE만 있다면 프로젝트 구동 가능, War는 웹서버 또는 Was가 있어야 구동 가능

#### 참고) 
[JAR WAR 차이점 및 특징](https://ifuwanna.tistory.com/224)
[Jar? War? 차이점 알아보자](https://joohoon.tistory.com/96)
