# [스프링 입문] Section 4. 스프링 빈과 의존관계

---

## **컴포넌트 스캔과 자동 의존관계 설정**

생성자에 @Autowired 가 있으면 스프링이 연관된 객체를 스프링 컨테이너에서 찾아서 넣어준다. 이렇게 객체 의존관계를 외부에서 넣어주는 것을 DI (Dependency Injection), 의존성 주입이라고 한다.

```java
@Controller
public class MemberController {

    private final MemberService memberService;

    @Autowired
    public MemberController(MemberService memberService) {
        this.memberService = memberService;
    }
}
```

**스프링 빈을 등록하는 2가지 방법**

- 컴포넌트 스캔과 자동 의존관계 설정
- 자바 코드로 직접 스프링 빈 동륵하기

**컴포넌트 스캔 원리**

- `@Component` 어노테이션이 있으면 스프링 빈으로 자동 등록된다.
- `@Controller` 컨트롤러가 스프링 빈으로 자동 등록된 이유도 컴포넌트 스캔 때문이다.

**`@Component`를 포함하는 다음 어노테이션도 스프링 빈으로 자동 등록된다.**

- `@Controller`
- `@Service`
- `@Repository`

```java
@Service
public class MemberService {

    private final MemberRepository memberRepository;

    @Autowired
    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
		//만약 생성자가 한개라면 이렇게 써도 무방하다.
		public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}
```

생성자가 1개일 경우에 @Autowired 는 생략 가능하다.

---

## 자바 코드로 직접 스프링 빈 등록하기

```java
@Configuration
public class SpringConfig {

    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
      return new MemoryMemberRepository();
    }
}
```

- XML 로 설정하는 방식도 있지만 최근에는 잘 사용하지 않는다
- [DI에는 필드,setter,생성자 주입이 있다.](https://github.com/GYEONGDONGBAEK/SpringStudy/blob/main/DI.md) 의존관계가 실행중에 동적으로 변하는 경우는 거의 없으므로 생성자 주입을 권장한다.
- @Autowired 를 통한 DI는 스프링이 관리하는 객체에서만 동작한다. 스프링 빈으로 등록하지 않고 내가 직접 생성한 객체에서는 동작하지 않는다.