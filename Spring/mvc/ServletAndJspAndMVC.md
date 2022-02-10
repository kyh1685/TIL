# 서블릿, JSP, MVC 패턴

_김영한 강사님의 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술 강의를 정리한 내용입니다._

## JSP
JSP에서는 자바 코드를 사용할 수 있다.
👉 실행하면 서버 내부에서 서블릿으로 변환시키기 때문

#### 라이브러리 추가
```java
// build.gradle에 추가
implementation 'org.apache.tomcat.embed:tomcat-embed-jasper'
implementation 'javax.servlet:jstl'
```

#### JSP 파일
```java
// jsp 첫 줄에 추가! JSP문서라는 뜻
<%@ page contentType="text/html;charset=UTF-8" language="java" %> 

// 자바의 import문
<%@ page import="hello.servlet.domain.member.MemberRepository" %>
// 자바 코드를 입력 
<% ~~ %>
// 자바 코드를 출력
<%= ~~ %>
// request나 model에 담긴 값을 꺼냄
${member.id}
// 첫 줄에 추가하면 taglib 기능을 쓸 수 있음
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
```

#### 🚨 서블릿과 JSP의 한계
- **서블릿** : 뷰 화면을 위해 HTML을 만드는 작업과 자바 코드가 섞여서 복잡함
- **JSP** : HTML 작업 처리, 동적으로 변경이 필요한 곳에만 자바 코드 적용
👉 **JSP가 혼자 너무 많은 역할을 하기 때문에 유지보수가 어려워짐**

## MVC 패턴
- **Controller** : HTTP 요청을 받아 파라미터를 검증하고, 비즈니스 로직을 실행한다. 뷰에 전달할 결과 데이터를 조회해서 모델에 담는다.
- **Model** : 뷰에 출력할 데이터를 담아둔다.
- **View** : 모델에 담겨있는 데이터를 사용해서 화면을 그린다.

#### 🤚 참고
컨트롤러에 비즈니스 로직을 두면 **컨트롤러가 너무 많은 역할을 담당**하기 때문에 **서비스 계층을 별도로 만들어 처리**한다. 컨트롤러는 비즈니스 로직이 있는 서비스를 호출한다. 

- **Service** : Controller에 의해 호출되어 비즈니스 로직과 트랜잭션을 처리하며 dao를 호출하여 DB CRUD를 처리 후 Controller로 반환
- **Dao** : Service에 의해 호출되어 DB 접근 담당
- **Dto** : 데이터 전송 객체(계층간 데이터를 전송할 때 사용)
- **Mapper** : Dao에서 요청하는 실제 쿼리문이 있는 곳

**🤔 Service와 Dao는 뭐가 다르지?**
Service는 사용자가 요청한 작업을 처리하는 과정을 하나로 묶은 것, Dao는 DB CRUD 작업을 하나씩 분할해 놓은 것

#### MVC 패턴1과 MVC 패턴 2의 차이점
- **MVC 패턴 1**
  - Controller와 View가 합쳐져 있는 구조
  - 개발하기 쉬우나 유지보수가 어려움
- **MVC 패턴 2** 
  - Controller, Model, View가 각각 따로 있는 구조
  - 유지보수와 확장이 용이하나 설계에 시간이 많이 듬
