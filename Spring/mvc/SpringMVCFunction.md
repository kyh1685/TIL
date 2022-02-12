_김영한 강사님의 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술 강의를 정리한 내용입니다._

## 요청 매핑
- **@Controller** : 반환 값이 String이면 뷰 이름으로 인식된다. 그래서 뷰를 찾고 뷰가 랜더링 된다.
- **@RestController** : 반환 값으로 뷰를 찾는 것이 아니라, HTTP 메시지 바디에 입력한다.  
- **@RequestMapping()**
  - url 호출이 오면 이 메서드가 실행되도록 매핑한다.
  - 대부분의 속성을 배열[] 로 제공하므로 다중 설정이 가능하다. {"/hello-basic", "/hello-go"}
- **PathVariable(경로 변수)**
```java
/** * PathVariable 사용
* 변수명이 같으면 생략 가능
* @PathVariable("userId") String userId -> @PathVariable userId
*/
@GetMapping("/mapping/{userId}")
public String mappingPath(@PathVariable("userId") String data) {
	log.info("mappingPath userId={}", data);
	return "ok";
}
```
- **특정 파라미터 조건 매핑**
```java
/**
 * 파라미터로 추가 매핑
 * params="mode",
 * params="!mode"
 * params="mode=debug"
 * params="mode!=debug" (! = )
 * params = {"mode=debug","data=good"}
 */
@GetMapping(value = "/mapping-param", params = "mode=debug")
public String mappingParam() {
	log.info("mappingParam");
	return "ok";
}
```
- **특정 헤더 조건 매핑**
```java
/**
 * 특정 헤더로 추가 매핑
 * headers="mode",
 * headers="!mode"
 * headers="mode=debug"
 * headers="mode!=debug" (! = )
 */
@GetMapping(value = "/mapping-header", headers = "mode=debug")
public String mappingHeader() {
	log.info("mappingHeader");
	return "ok";
}
```
- **미디어 타입 조건 매핑 - HTTP 요청 Content-Type, consume**
```java
/**
 * Content-Type 헤더 기반 추가 매핑 Media Type
 * consumes="application/json"
 * consumes="!application/json"
 * consumes="application/*"
 * consumes="*\/*"
 * MediaType.APPLICATION_JSON_VALUE
 */
@PostMapping(value = "/mapping-consume", consumes = "application/json")
public String mappingConsumes() {
 	log.info("mappingConsumes");
 	return "ok";
}
```
- **미디어 타입 조건 매핑 - HTTP 요청 Accept, produce**
```java
/** * Accept 헤더 기반 Media Type
 * produces = "text/html"
 * produces = "!text/html"
 * produces = "text/*"
 * produces = "*\/*"
 */
@PostMapping(value = "/mapping-produce", produces = "text/html")
public String mappingProduces() {
	log.info("mappingProduces");
	return "ok";
}
```

## HTTP 요청 - 기본, 헤더 조회
```java
@RequestMapping("/headers")
public String headers(HttpServletRequest request,
					  HttpServletResponse response,
					  HttpMethod httpMethod, 
					  Locale locale,
					  @RequestHeader MultiValueMap<String, String> headerMap,
					  @RequestHeader("host") String host,
					  @CookieValue(value = "myCookie"
                      			 , required = false) String cookie) {...}
```
- **HttpMethod** : HTTP 메서드 조회
- **Locale** : Locale 정보 조회
- **@RequestHeader MultiValueMap<String, String> headerMap** : 모든 HTTP 헤더 MultiValueMap 형식으로 조회
- **@RequestHeader("host") String host** : 특정 HTTP 헤더 조회
  - 필수 값 여부 : required
  - 기본 값 속성 : defaultValue
- **@CookieValue(value = "myCookie", required = false) String cookie** : 특정 쿠키 조회
  - 필수 값 여부 : required
  - 기본 값 : defaultValue
- **MultiValueMap** : MAP과 유사, 하나의 키에 여러 값을 받음
  - HTTP header, HTTP 쿼리 파라미터와 같이 하나의 키에 여러 값을 받을 때 사용
  - keyA=value1&keyA=value2
  
## HTTP 요청 파라미터 

### @RequestParam
```java
/**
 * @RequestParam 사용
 * - 파라미터 이름으로 바인딩
 * - 파라미터 이름이 변수와 같으면 @RequestParam(name="xx") 생략 가능
 * - String , int 등의 단순 타입이면 @RequestParam도 생략 가능
 * - @RequestParam(required = true) : 파라미터 필수 여부, 기본값이 true
 * @ResponseBody 추가
 * - View 조회를 무시하고, HTTP message body에 직접 해당 내용 입력
 */
@ResponseBody
@RequestMapping("/request-param-v2")
public String requestParamV2(@RequestParam("username") String memberName
						   , @RequestParam("age") int memberAge) {
	log.info("username={}, age={}", memberName, memberAge);
	return "ok";
}
```
**🚨 주의**
- 파라미터 이름만 있고 값이 없는 경우 빈문자로 통과
예) /request-param?username=
- null을 기본형(int)에 입력하는 것은 불가능(500 예외 발생)
👉 null을 받을 수 있는 Integer로 변경하거나, defaultValue 속성 사용

### @ModelAttribute
```java
/** 
 * @ModelAttribute
 * - model.addAttribute(helloData) 코드도 함께 자동 적용됨
 * - @ModelAttribute 생략 가능
 * - String, int 같은 단순 타입 = @RequestParam
 * - argument resolver 로 지정해둔 타입 외 = @ModelAttribute
 */
@ResponseBody
@RequestMapping("/model-attribute-v1")
public String modelAttributeV1(@ModelAttribute HelloData helloData) {
	log.info("username={}, age={}", helloData.getUsername(),
	helloData.getAge());
 	
    return "ok";
}
```
- 스프링MVC는 @ModelAttribute가 있으면 HelloData 객체를 생성한다.
- 요청 파라미터의 이름으로 HelloData 객체의 속성을 찾는다. 
- 해당 속성의 setter를 호출해서 파라미터의 값을 입력(바인딩) 한다.
예) 파라미터 이름이 username이면 setUsername()메서드를 찾아서 호출하면서 값을 입력

**스프링이 생략시 따르는 규칙**
- String , int , Integer 같은 단순 타입 = @RequestParam
- 나머지 = @ModelAttribute (argument resolver 로 지정해둔 타입 외)

## HTTP 요청 메세지
요청 파라미터와 다르게,** HTTP 메시지 바디를 통해 직접 데이터가 넘어오는 경우 @RequestParam, @ModelAttribute를 사용할 수 없다.**

### 단순 텍스트
```java
/**
 * @RequestBody
 * - 메시지 바디 정보를 직접 조회(@RequestParam X, @ModelAttribute X)
 * - HttpMessageConverter 사용 -> StringHttpMessageConverter 적용
 *
 * @ResponseBody
 * - 메시지 바디 정보 직접 반환(view 조회X)
 * - HttpMessageConverter 사용 -> StringHttpMessageConverter 적용
 */
@ResponseBody
@PostMapping("/request-body-string-v4")
public String requestBodyStringV4(@RequestBody String messageBody) {
	log.info("messageBody={}", messageBody);
	return "ok";
}
```

#### 📌 정리
- 요청 파라미터를 조회 : @RequestParam , @ModelAttribute
- HTTP 메시지 바디 직접 조회: @RequestBody

### JSON
HTTP API에서 JSON 데이터 형식을 주로 사용한다.

```java
/**
 * @RequestBody 생략 불가능(@ModelAttribute 가 적용되어 버림)
 * HttpMessageConverter 사용 
 * -> MappingJackson2HttpMessageConverter(contenttype:application/json)
 */
@ResponseBody
@PostMapping("/request-body-json-v3")
public String requestBodyJsonV3(@RequestBody HelloData data) {
	log.info("username={}, age={}", data.getUsername(), data.getAge());
	return data;
}
```
- @RequestBody에 직접 만든 객체를 지정할 수 있다.
- HttpEntity, @RequestBody를 사용하면 HTTP 메시지 컨버터가 HTTP 메시지 바디의 내용을 원하는 문자나 객체 등으로 변환해준다.
- 응답의 경우에도 @ResponseBody를 사용하면 해당 객체를 HTTP 메시지 바디에 직접 넣어줄 수 있다.
![](https://images.velog.io/images/3hee_11/post/019c8621-9dc8-4591-8383-db1d5136699a/image.png)
**🚨 주의**
@ModelAttribute도 생략이 가능하기 때문에 @RequestBody \를 생략하면 @ModelAttribute가 적용된다.

## HTTP 응답 

### 정적 리소스
웹 브라우저에 정적인 HTML, css, js을 제공할 때 정적 리소스를 사용
- **정적 리소스 경로** : src/main/resources/static

### 뷰 템플릿
웹 브라우저에 동적인 HTML을 제공할 때 뷰 템플릿 사용하며, 뷰 템플릿을 거쳐서 HTML이 생성되고, 뷰가 응답을 만들어서 전달한다.
- **뷰 템플릿 경로** : src/main/resources/templates

#### Thymeleaf 스프링 부트 설정
```java
// 경로 : build.gradle
`implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'`
```
- 스프링 부트가 자동으로 ThymeleafViewResolver와 필요한 스프링 빈들을 등록
```java
// 경로 : application.properties
// 아래 설정은 기본 값이기 때문에 변경이 필요할 때만 별도로 설정
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
```

### HTTP API, 메시지 바디에 직접 입력
HTML이나 뷰 템플릿을 사용해도 HTTP 응답 메시지 바디에 HTML 데이터가 담겨서 전달된다. 여기서 설명하는 내용은 정적 리소스나 뷰 템플릿을 거치지 않고, **직접 HTTP 응답 메시지를 전달하는 경우**를 말한다.

```java
@ResponseStatus(HttpStatus.OK)
@ResponseBody
@GetMapping("/response-body-json-v2")
public HelloData responseBodyJsonV2() {
	HelloData helloData = new HelloData();
	helloData.setUsername("userA");
	helloData.setAge(20);
    
	return helloData;
}
```

## HTTP 메시지 컨버터

#### @ResponseBody 사용 원리
![](https://images.velog.io/images/3hee_11/post/5da4aec4-fdad-47a8-9de9-1686a727db2b/image.png)

- @ResponseBody 사용
- HTTP의 BODY에 문자 내용을 직접 반환
- viewResolver 대신 HttpMessageConverter 동작
  - 기본 문자처리 : StringHttpMessageConverter
  - 기본 객체처리: MappingJackson2HttpMessageConverter
- byte 처리 등등 기타 여러 HttpMessageConverter가 기본으로 등록되어 있음

#### 스프링 MVC가 HTTP 메시지 컨버터를 적용하는 경우
- HTTP 요청: @RequestBody , HttpEntity(RequestEntity) 
- HTTP 응답: @ResponseBody , HttpEntity(ResponseEntity)

#### HTTP 요청 데이터 읽기
- HTTP 요청이 오고 컨트롤러에서 @RequestBody, HttpEntity 파라미터 사용
- 메시지 컨버터가 메시지를 읽을 수 있는지 확인하기 위해 canRead() 호출
  - 대상 클래스 타입을 지원하는가. 
  예) @RequestBody의 대상 클래스 (byte[], String, HelloData )
  - HTTP 요청의 Content-Type 미디어 타입을 지원하는가.
  예) text/plain , application/json , */*
- canRead() 조건을 만족하면 read()를 호출해서 객체 생성하고 반환

#### HTTP 응답 데이터 생성
- 컨트롤러에서 @ResponseBody, HttpEntity로 값이 반환됨
- 메시지 컨버터가 메시지를 쓸 수 있는지 확인하기 위해 canWrite() 호출
  - 대상 클래스 타입을 지원하는가.
  예) return의 대상 클래스 ( byte[] , String , HelloData )
  - HTTP 요청의 Accept 미디어 타입을 지원하는가. 
  예) text/plain , application/json , */*
- canWrite() 조건을 만족하면 write()를 호출해서 HTTP 응답 메시지 바디에 데이터를 생성

## 요청 매핑 헨들러 어댑터 구조

#### 🔸 RequestMappingHandlerAdaptor 동작 방식
![](https://images.velog.io/images/3hee_11/post/44e4404e-bd7c-4b8e-9d63-116c12792318/image.png)

### ArgumentResolver
애노테이션 기반 컨트롤러를 처리하는 RequestMappingHandlerAdaptor는 **ArgumentResolver를 호출하여 컨트롤러에게 필요한 값을 생성**한다.
~~HandlerMethodArgumentResolver를 줄여서 ArgumentResolver라고 함~~

#### 🔹 ArgumentResolver 동작 방식
ArgumentResolver의 supportsParameter()를 호출해서 **해당 파라미터를 지원하는지 체크**하고, 지원하면 resolveArgument()를 호출해서 **객체를 생성**한다. 그리고 생성된 객체를 컨트롤러 호출시 넘긴다.

### ReturnValueHandler
ArgumentResolver와 비슷하게 응답 값을 변환하고 처리한다.
~~HandlerMethodReturnValueHandler를 줄여서 ReturnValueHandle라고 함~~ 

### HTTP 메시지 컨버터

#### 메세지 컨버터 위치
![](https://images.velog.io/images/3hee_11/post/f4698c20-0f2c-4009-b151-d8d15f69c2ac/image.png)
- **요청의 경우** 각각 @RequestBody, HttpEntity를 처리하는 **ArgumentResolver**가 있고 HTTP 메시지 컨버터를 사용해 필요한 객체를 생성한다.
- **응답의 경우** @ResponseBody와 HttpEntity를 처리하는 **ReturnValueHandler**가 있고 HTTP 메시지 컨버터를 호출해서 응답 결과를 만든다.

_스프링 MVC는 @RequestBody, @ResponseBody가 있으면 ArgumentResolver로 RequestResponseBodyMethodProcessor를 사용하고 HttpEntity가 있으면 HttpEntityMethodProcessor를 사용한다._
