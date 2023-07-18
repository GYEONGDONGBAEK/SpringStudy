# [스프링 입문] Section 5. 회원 관리 예제 - 웹 MVC 개발

---

## ****회원 웹 기능 -  홈 화면 추가****

```java
@Controller
public class HomeController {

    @GetMapping("/")
    public String home() {
        return "home";
    }
}
```

위의 코드는 "/" 경로로 들어올 때 home 이 호출이 된다는 의미다.

home.html 파일을 만들어 주면 “/” 경로로 들어올때 이 파일이 호출될 것이다.

만약 Controller에 기본 경로인 "/" 로 들어왔을 때의 함수를 정의해두지 않는다면 static폴더에 있는 index.html 이 열리게 된다.

### home.html 파일

```java
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
</html
```

---

## 회원 웹 기능- 등록

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

위에 작성한 homl.html 에서 회원 가입을 클릭 한다면 createForm 이라는 메서드가 실행이 되고 createMemberForm 으로 이동하게 될 것이다.

### createMemberForm.html 파일

```java
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org"><body>
<div class="container">
    <form action="/members/new" method="post">
        <div class="form-group">
            <label for="name">이름</label>
            <input type="text" id="name" name="name" placeholder="이름을 입력하세요">
        </div>
        <button type="submit">등록</button>
    </form>
</div> <!-- /container -->
</body>
</html>
```

input창안에 이름을 입력하고 등록버튼을 누르면 name이라는 key와 input창에 입력한 값이 value로 서버에 넘어간다.

### 웹 등록 화면에서 데이터를 전달 받을 폼 객체

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

createMemberForm 에서 작성한 이름이 value로 서버에 넘어온다. 이 때 createMemberForm 에서 key 값과 MemberForm에 있는 key 값이 동일 하다면 자동으로 매칭된다.

### 회원 컨트롤러에서 회원을 실제 등록하는 기능

```java
@PostMapping("/members/new")
public String create(MemberForm form){
    Member member = new Member();
    member.setName(form.getName());
 
    memberService.join(member);
 
    return "redirect:/";
}
```

이 코드가 어떻게 동작하냐면,

이름을 spring으로 입력하고 등록하면, creawteMemberForm.html에 있는 form태그안에 method가 post이기 때문에 post방식으로 "/members/new" 경로로 name="name" 이고 value=""들어오게 된다. 이 후 member.setName(form.getName()); 을 거쳐 name=”name” , value=”spring” 으로 변하게 되고 memberService.join 메서드를 호출한다. 이 후 모든 로직이 끝나면 “/” 로 redirect 하게 된다.

---

## 회원 웹 기능- 조회

```java
@GetMapping(value = "/members")
public String list(Model model) {
 List<Member> members = memberService.findMembers();
 model.addAttribute("members", members);
 return "members/memberList";
}
```

멤버 목록을 받아서 model에 넣어주고 멤버를 조회하는 주소를 반환해주는 코드이다.

model에 데이터를 넣어주면 반환 주소로 갈 때 그 데이터를 가지고 이동해서 이동한 페이지에서 해당 데이터를 사용할 수 있다.

```java
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

모델에 회원 목록을 담아왔기 때문에 memberList.html에서 members라는 데이터를 사용할 수 있는 것이다. ${변수명} 을 이용해서 데이터를 사용할 수 있다.

회원 정보가 메모리에 있기 때문에 서버를 재가동하면 회원 목록은 초기화 된다. 그렇기 때문에 파일이나 DB에 저장해야한다.