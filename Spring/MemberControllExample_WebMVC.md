# 회원 관리 예제 - 웹 MVC 개발

_김영한 강사님의 스프링 입문 강의를 정리한 내용입니다._

### 회원 웹 기능 - 홈 화면 추가

```java
@Controller
public class HomeController {
 	@GetMapping("/")
 	public String home() {
 		return "home";
 	}
}
```

```
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<body>
<div class="container">
 <div>
 <h1>Hello Spring</h1>
 <p>회원 기능</p>
 <p>
 <a href="/members/new">회원 가입</a>
 <a href="/members">회원 목록</a>
 </p>
 </div></div> <!-- /container -->
</body>
</html>
```

### 회원 웹 기능 - 등록
```java
@Controller
public class MemberController {
 	private final MemberService memberService;
 	
    	@Autowired
 	public MemberController(MemberService memberService) {
 		this.memberService = memberService;
 	}
 	@GetMapping(value = "/members/new")
 	public String createForm() {
 		return "members/createMemberForm";
 	}
}
```

- resources/templates/members/createMemberForm )
```
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org"><body>
<div class="container">
 <form action="/members/new" method="post">
 <div class="form-group">
 <label for="name">이름</label>
 <input type="text" id="name" name="name" placeholder="이름을
입력하세요">
 </div>
 <button type="submit">등록</button>
 </form>
</div> <!-- /container -->
</body>
</html>
```

- 데이터를 전달받을 객체
```java
public class MemberForm {
 	private String name;
 
 	public String getName() {
 		return name;
 	}
 	public void setName(String name) {
 		this.name = name;
 	}
}
```

- Controller에서 실제 등록하는 기능
```java
@PostMapping(value = "/members/new")
	public String create(MemberForm form) {
 		Member member = new Member();
 		member.setName(form.getName());
 		memberService.join(member);
 		return "redirect:/";
}
```
### 회원 웹 기능 - 조회
- Controller에 조회 기능 추가
```java
@GetMapping(value = "/members")
public String list(Model model) {
 	List<Member> members = memberService.findMembers();
 	model.addAttribute("members", members);
 	return "members/memberList";
}
```
- 회원 리스트 화면
```
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<body>
<div class="container">
 <div>
 <table>
 <thead> <tr>
 <th>#</th>
 <th>이름</th>
 </tr>
 </thead>
 <tbody>
 <tr th:each="member : ${members}">
 <td th:text="${member.id}"></td>
 <td th:text="${member.name}"></td>
 </tr>
 </tbody>
 </table>
 </div>
</div> <!-- /container -->
</body>
</html>
```
