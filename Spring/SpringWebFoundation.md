# 스프링 웹 개발 기초

*김영한 강사님의 스프링 입문 강의를 정리한 내용입니다.*

#### 정적 컨텐츠
정적 컨텐츠란 html 그대로 서버에 전달되는 것을 의미한다.
![](https://images.velog.io/images/3hee_11/post/5257f75d-f8bd-41b8-9c08-f22a6020648a/image.png)
그림처럼 hello-static.html에 접근하려고 하면 먼저 컨트롤러 중에서 hello-static이 있는지 찾고 없으면 resources 안에 있는 static/hello-static.html을 반환해준다.

#### MVC와 템플릿 엔진
- Model : 데이터와 비즈니스 로직 관리
- View : 레이아웃과 화면 처리
- Controller : 명령을 Model과 View로 라우팅

Controller
```java
@Controller
public class HelloController {

 @GetMapping("hello-mvc")
 public String helloMvc(@RequestParam("name") String name, Model model) {
 	model.addAttribute("name", name);
 	return "hello-template";
 }
}
```
View
< resources/template/hello-template.html >
```
<html xmlns:th="http://www.thymeleaf.org">
<body>
<p th:text="'hello ' + ${name}">hello! empty</p>
</body>
</html>
```
![](https://images.velog.io/images/3hee_11/post/0397d57e-63ea-470e-8614-6669d64cd275/image.png)

위에 정적 컨텐츠를 설명했던 것처럼 hello-mvc를 검색하면 일단 컨트롤러에서 찾게되고 컨트롤러에 hello-mvc가 있으니까 model에 데이터를 담아서 hello-template.html로 리턴해준다.

#### API
**@ResponseBody 문자 반환**
```java
@Controller
public class HelloController {
 @GetMapping("hello-string")
 @ResponseBody
 public String helloString(@RequestParam("name") String name) {
 	return "hello " + name;
 }
}
```
- @ResponseBody를 사용하면  viewResolver를 사용하지 않는다.
- 대신에 아래 그림처럼 HTTP의 BODY에 문자 내용을 직접 반환한다.
![](https://images.velog.io/images/3hee_11/post/639d563d-1cb5-4216-9de3-8a82032e3c08/image.png)

**@ResponseBody 객체 반환**
```java
@Controller
public class HelloController {
 @GetMapping("hello-api")
 @ResponseBody
 public Hello helloApi(@RequestParam("name") String name) {
 	Hello hello = new Hello();
 	hello.setName(name);
 	return hello;
 }
 
 static class Hello {
 	private String name;
 	
    public String getName() {
 		return name;
 	}
 	public void setName(String name) {
 		this.name = name;
 	}
 }
}
```
- @ResponseBody 를 사용하고, 객체를 반환하면 객체가 JSON으로 변환됨
![](https://images.velog.io/images/3hee_11/post/4d7b4803-d959-4589-b5c8-a7eb8d3d5727/image.png)

**@ResponseBody 사용 원리**
![](https://images.velog.io/images/3hee_11/post/0ff9902d-899a-45c1-b05a-1c9ff323b311/image.png)
@ResponseBody가 붙으면 HTTP의 BODY에 문자 내용을 직접 반환하는데 viewResolver 대신에 HttpMessageConverter 가 동작한다.
- 기본 문자처리: StringHttpMessageConverter
- 기본 객체처리: MappingJackson2HttpMessageConverter
- byte 처리 등등 기타 여러 HttpMessageConverter가 기본으로 등록되어 있다.
