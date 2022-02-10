# 서블릿

_김영한 강사님의 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술 강의를 정리한 내용입니다._

#### 🚨 주의
- **인텔리제이 커뮤니티 버전의 경우 패키징이 War로 되어 있으면 Run 설정 부분을 Gradle로 설정해야한다!**
- 혹은 build.gradle에 있는 providedRuntime 'org.springframework.boot:spring-boot-starter-tomcat' 제거하기
- Jar는 문제가 없지만 War의 경우 톰캣이 정상 시작되지 않는 문제가 발생함
_여기서 War를 사용하는 이유는 jsp와 servlet 사용하려고_

#### [🤔 War와 Jar란 무엇일까?](https://velog.io/@3hee_11/War%EC%99%80-Jar%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C)

![](https://images.velog.io/images/3hee_11/post/e64af305-c2f3-4bfd-ae66-dae07116469c/image.png)

**_~ 참고 ~_**
- _서블릿은 톰캣 같은 웹 애플리케이션 서버를 직접 설치하고,그 위에 서블릿 코드를 클래스 파일로 빌드해서 올린 다음, 톰캣 서버를 실행하면 된다. _
- _스프링 부트는 톰캣 서버를 내장하고 있으므로, 톰캣 서버 설치 없이 편리하게 서블릿 코드를 실행할 수 있다._

## 서블릿 등록
- 스프링 부트는 서블릿을 직접 등록해서 사용할 수 있는 어노테이션을 지원한다.
👉 @ServletComponentScan
- 스프링이 자동으로 현재 내 패키지 포함 하위폴더를 뒤져서 서블릿을 찾고 등록해준다.
```java
// 경로 : hello.servlet.ServletApplication
@ServletComponentScan 
@SpringBootApplication
public class ServletApplication {
	...
}
```
```java
// 경로 : hello.servlet.basic.HelloServlet
@WebServlet(name = "helloServlet", urlPatterns = "/hello")
public class HelloServlet extends HttpServlet {
 	
    // @WebServlet : 서블릿 애노테이션
	// name : 서블릿 이름
	// urlPatterns : URL 매핑
    
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
     	String username = request.getParameter("username");
     	
        response.setContentType("text/plain");
     	response.setCharacterEncoding("utf-8");
        
     	response.getWriter().write("hello " + username);
    }
}
```
**⭐ HTTP 요청을 통해 매핑된 URL이 호출되면 서블릿 컨테이너는 service 메서드 실행**

## 서블릿 컨테이너 동작 방식

#### 내장 톰캣 서버 생성
![](https://images.velog.io/images/3hee_11/post/8a5cf269-d9a3-4911-923a-8644bb99272d/image.png)

#### 웹 애플리케이션 서버의 요청 응답 구조
![](https://images.velog.io/images/3hee_11/post/6305f88f-51e0-400b-8c0a-75df8624c42b/image.png)

- 클라이언트가 URL을 통해 요청을 보내면 HTTP Request를 서블릿 컨테이너로 전송한다(주소를 웹서버가 받아 서블릿 컨테이너로 전송)
- HTTP Request를 전송받은 서블릿 컨테이너는 HttpServletRequest, HTTPServletResponse 객체를 생성한다.
  👉 각각 클라이언트가 요청한 정보를 가져오고 서버가 응답할 정보를 넣음
- 요청 URL을 분석하여 어떤 서블릿을 호출할 것인지 조회한다.
👉 서블릿 컨테이너에서 실행된 적이 있는지 확인하고, 있다면 인스턴스 생성 없다면 기존 인스턴스 사용
✔ 서블릿 인스턴스는 서블릿 컨테이너 당 하나씩 존재
- 찾아온 서블릿에서 service()를 호출한다.
👉 HttpServletRequest, HttpServletResponse 객체를 인자로 전달
- 서블릿은 service()의 결과를  HttpServlet Response 객체에 담아 응답을 보낸다.
- 응답이 끝나면 Request , Response 객체를 소멸시킨다.

**참고)**
[[WEB] 서블릿(Servlet) 과 서블릿 컨테이너(Servlet Container)](https://maenco.tistory.com/entry/%EC%84%9C%EB%B8%94%EB%A6%BFServlet-%EA%B3%BC-%EC%84%9C%EB%B8%94%EB%A6%BF-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88Servlet-Container)
[Servlet의 개념과 동작 과정](https://programmingnote.tistory.com/61)

**_~ 참고 ~_**
main/webapp/ 경로에 index.html을 두면 로컬호스트 호출 시 인덱스 페이지가 열린다.

#### HTTP 요청 메시지 로그로 확인하기
```java
// application.properties에 아래 문구 
// 모든 요청 정보를 남기면 성능 저하가 될 수 있으므로 개발 단계에서만 사용(운영 X)
logging.level.org.apache.coyote.http11=debug
```

## HttpServletRequest 
서블릿은 HTTP 요청 메시지를 파싱해서 HttpServletRequest 객체에 담아 제공한다.

#### Http 요청 메세지
```java
POST /save HTTP/1.1
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded

username=kim&age=20
```
- START LINE : HTTP 메소드, URL, 쿼리 스트링, 스키마, 프로토콜
- 헤더 : 헤더 조회
- 바디 : form 파라미터 형식 조회, message body 데이터 직접 조회

#### 임시 저장소 기능
해당 HTTP 요청이 시작부터 끝날 때 까지 유지되는 임시 저장소 기능
- **저장** : request.setAttribute(name, value)
- **조회** : request.getAttribute(name)

#### 세션 관리 기능
- request.getSession(create: true)

## Http 요청 데이터
HTTP 요청 메시지를 통해 클라이언트에서 서버로 데이터를 전달하는 방법

### GET - 쿼리 파라미터
- /url?username=hello&age=20
- 메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함해서 전달
예) 검색, 필터, 페이징 등에서 많이 사용하는 방식

#### 쿼리 파라미터 조회
```java
// 단일 파라미터 조회
String username = request.getParameter("username"); 
// 파라미터 이름들
Enumeration<String> parameterNames = request.getParameterNames(); 

// 모두 조회
//파라미터를 Map으로 조회
Map<String, String[]> parameterMap = request.getParameterMap(); 
//복수 파라미터 조회
String[] usernames = request.getParameterValues("username"); 
```

### POST - HTML Form
- content-type: application/x-www-form-urlencoded
- 메시지 바디에 쿼리 파리미터 형식으로 전달 username=hello&age=20
예) 회원 가입, 상품 주문, HTML Form 사용

_서버에서 조회는 쿼리 파라미터와 같음_

### HTTP message body에 담기
- HTTP API에서 주로 사용, JSON, XML, TEXT
- 데이터 형식은 주로 JSON 사용
- POST, PUT, PATCH

#### 단순 텍스트
- inputStream은 byte 코드를 반환하기 때문에 문자열로 읽을 수 있도록 UTF_8 Charset을 지정한다.
```java
ServletInputStream inputStream = request.getInputStream();
String messageBody = StreamUtils.copyToString(inputStream,
StandardCharsets.UTF_8);
```
#### Json 
- messageBody를 출력해보면 json 형태로 나오기 때문에 HelloData로 파싱
```java
private ObjectMapper objectMapper = new ObjectMapper();
 
ServletInputStream inputStream = request.getInputStream();
String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);
```

## Http 응답 데이터

#### 단순 텍스트
```java
writer.println("ok");
```

#### html
- HTTP 응답으로 HTML을 반환할 때는 content-type을 text/html로 지정해야 함
```java
response.setContentType("text/html");
response.setCharacterEncoding("utf-8");

PrintWriter writer = response.getWriter();
writer.println("<html>");
writer.println("<body>");
writer.println(" <div>안녕?</div>");
writer.println("</body>");
writer.println("</html>");
```

#### HTTP API - MessageBody JSON 
- HTTP 응답으로 JSON을 반환할 때는 content-type을 application/json로 지정해야 함
```java
response.setHeader("content-type", "application/json");
response.setCharacterEncoding("utf-8");

HelloData data = new HelloData();
data.setUsername("kim"); 
data.setAge(20);

String result = objectMapper.writeValueAsString(data);
response.getWriter().write(result);
```
