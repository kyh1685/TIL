_김영한 강사님의 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술 강의를 정리한 내용입니다._

## 스프링 MVC 전체 구조
![](https://images.velog.io/images/3hee_11/post/c1cb308e-ddfa-46ed-be45-cc232dc62801/image.png)

### DispacherServlet
스프링 MVC의 프론트 컨트롤러가 디스패처 서블릿이다.

#### DispacherServlet 서블릿 등록
- DispacherServlet은 부모 클래스에서 HttpServlet을 상속 받아서 사용하고, 서블릿으로 동작한다.
  - DispatcherServlet → FrameworkServlet → HttpServletBean → HttpServlet
- 스프링부트는 DispacherServlet을 서블릿으로 자동으로 등록하면서 모든 경로( urlPatterns="/" )에 대해서 매핑한다.

#### 요청 흐름
- 서블릿이 호출되면 HttpServlet이 제공하는 serivce()가 호출된다.
- 스프링 MVC는 DispatcherServlet의 부모인 FrameworkServlet에서 service()를 오버라이드해두었다.
- FrameworkServlet.service()를 시작으로 여러 메서드가 호출되면서 DispacherServlet.doDispatch()가 호출된다.

```java
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {	
    HttpServletRequest processedRequest = request;
	HandlerExecutionChain mappedHandler = null;
	ModelAndView mv = null;
    
	// 1. 핸들러 조회
	mappedHandler = getHandler(processedRequest);
	if (mappedHandler == null) {
    	noHandlerFound(processedRequest, response);
		return;
	}
    
	// 2. 핸들러 어댑터 조회 - 핸들러를 처리할 수 있는 어댑터
	HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());
	
    // 3. 핸들러 어댑터 실행 -> 4. 핸들러 어댑터를 통해 핸들러 실행 -> 5. ModelAndView 반환
	mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
	processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
}

private void processDispatchResult(HttpServletRequest request, HttpServletResponse response, HandlerExecutionChain mappedHandler, ModelAndView mv, Exception exception) throws Exception {
    // 뷰 렌더링 호출
	render(mv, request, response);
}

protected void render(ModelAndView mv, HttpServletRequest request, HttpServletResponse response) throws Exception {
	View view;
	String viewName = mv.getViewName();
    
	// 6. 뷰 리졸버를 통해서 뷰 찾기, 7. View 반환
	view = resolveViewName(viewName, mv.getModelInternal(), locale, request);
	
    // 8. 뷰 렌더링
	view.render(mv.getModelInternal(), request, response);
}
```

#### 동작 순서
- **핸들러 조회** : 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러(컨트롤러)를 조회한다.
- **핸들러 어댑터 조회** : 핸들러를 실행할 수 있는 핸들러 어댑터를 조회한다.
- **핸들러 어댑터 실행** : 핸들러 어댑터를 실행한다.
- **핸들러 실행 **: 핸들러 어댑터가 실제 핸들러를 실행한다.
-** ModelAndView 반환** : 핸들러 어댑터는 핸들러가 반환하는 정보를 ModelAndView로 변환해서 반환한다.
- **viewResolver 호출** : 뷰 리졸버를 찾고 실행한다.
  - JSP의 경우 InternalResourceViewResolver가 자동 등록되고, 사용된다.
- **View 반환** : 뷰 리졸버는 뷰의 논리 이름을 물리 이름으로 바꾸고, 렌더링 역할을 담당하는 뷰 객체를 반환한다.
  - JSP의 경우 InternalResourceView(JstlView)를 반환하는데, 내부에 forward() 로직이 있다.
- **뷰 렌더링** : 뷰를 통해서 뷰를 렌더링 한다

## 스프링 MVC
- **@Controller** : 스프링이 자동으로 스프링 빈으로 등록한다. 
  - 내부에 @Component 애노테이션이 있어서 컴포넌트 스캔의 대상이 됨
  - 스프링 MVC에서 애노테이션 기반 컨트롤러로 인식한다.
- **@RequestMapping** : 요청 정보를 매핑한다. 해당 URL이 호출되면 이 메서드가 호출된다. 
- **ModelAndView** : 모델과 뷰 정보를 담아서 반환하면 된다.
- **ViewName 직접 반환** : 뷰의 논리 이름을 반환할 수 있다.
- **@RequestParam** : HTTP 요청 파라미터를 @RequestParam으로 받을 수 있다.
  - request.getParameter()와 비슷한 코드
- **Model**
- @**RequestMapping → @GetMapping, @PostMapping**
