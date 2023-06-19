# DI(Dependency Injection)

---

# ****의존성 주입(DI)란****

---

외부에서 두 객체 간의 관계를 결정해주는 디자인 패턴으로, 인터페이스를 사이에 둬서 클래스 레벨에서는 의존관계가 고정되지 않도록 하고 런타임 시에 관계를 동적으로 주입하여 유연성을 확보하고 결합도를 낮출 수 있게 해주는 방법.

---

## 의존성?

의존성이란 한 객체가 다른 객체를 사용할 때 의존성이 있다고 한다. 

예를 들어 다음과 같이 SideProject 객체가 backend 객체를 사용하고 있는 경우에 우리는 SideProject객체가 backend 객체에 의존성이 있다고 표현한다.

<aside>
💡

public class SideProject {

private backend backend;

}

</aside>

이와 같이 코드를 설계하였을 때, 코드의 재활용성이 떨어지고, 위 예제에서 backend 클래스가 수정 되었을 때, SideProject 클래스도 함께 수정해줘야하는 문제가 발생한다.

즉, 결합도(coupling)가 높아지게 된다.

---

# 의존성 주입 예시

```java
public interface People{

}

public class Backend implements People{

}
```

이제 SideProject 클래스의 생성자에서 외부로부터 People을 주입(injection) 받도록 변경

```java
public class SideProject{

	private People people;

	public Backend(People people) {
		this.people = people;
	}

}
```

이때 스프링의 DI 컨테이너가 애플리케이션 실행 시점에 필요한 객체를 생성하여 SideProject 클래스에 주입해주는 역할을 한다. 예를 들어 다음과 같이 동작한다.

```java
// DI 컨테이너에서의 동작

SideProject = new SideProject(new Backend());

//프론트 엔드 개발자가 필요하다면 ?

SideProject = new SideProject(new Frontend());

```

이렇게 하면 는 객체가 바뀌더라도 생성자를 변경하지 않아도 된다. 그저 객체가 바뀐다면 외부에서 바뀐 객체를 주입해주기만 하면 된다.

---

# 의존성 주입 방법

### **[ 1. 생성자 주입(Constructor Injection) ]**

생성자 주입(Constructor Injection)은 생성자를 통해 의존 관계를 주입하는 방법이다.

```java
@Service
public class UserService {

    private UserRepository userRepository;
    private MemberService memberService;

    @Autowired
    public UserService(UserRepository userRepository, MemberService memberService) {
        this.userRepository = userRepository;
        this.memberService = memberService;
    }

}
```

생성자 주입은 생성자의 호출 시점에 1회 호출 되는 것이 보장된다. 그렇기 때문에 주입받은 객체가 변하지 않거나, 반드시 객체의 주입이 필요한 경우에 강제하기 위해 사용할 수 있다. 또한 Spring 프레임워크에서는 생성자 주입을 적극 지원하고 있기 때문에, 생성자가 1개만 있을 경우에 @Autowired를 생략해도 주입이 가능하도록 편의성을 제공하고 있다. 그렇기 때문에 위의 코드는 아래와 동일한 코드가 된다.

```java
@Service
public class UserService {

    private UserRepository userRepository;
    private MemberService memberService;

    public UserService(UserRepository userRepository, MemberService memberService) {
        this.userRepository = userRepository;
        this.memberService = memberService;
    }

}
```

### **[ 2. 수정자 주입(Setter 주입, Setter Injection) ]**

수정자 주입(Setter 주입, Setter Injection)은 필드 값을 변경하는 Setter를 통해서 의존 관계를 주입하는 방법이다. Setter 주입은 생성자 주입과 다르게 주입받는 객체가 변경될 가능성이 있는 경우에 사용한다. (실제로 변경이 필요한 경우는 극히 드물다.)

```java
@Service
public class UserService {

    private UserRepository userRepository;
    private MemberService memberService;

    @Autowired
    public void setUserRepository(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    @Autowired
    public void setMemberService(MemberService memberService) {
        this.memberService = memberService;
    }
}
```

@Autowired로 주입할 대상이 없는 경우에는 오류가 발생한다. 위의 예제에서는 XXX 빈이 존재하지 않을 경우에 오류가 발생하는 것이다. 주입할 대상이 없어도 동작하도록 하려면 @Autowired(required = false)를 통해 설정할 수 있다.

단점으로는 Setter가 Public으로 선언이 되기때문에 다른 사람이 코드에 접근하기 쉬우므로, 코드가 변경될 수도 있다는 점이 가장 큰 단점이다.

### **[ 3. 필드 주입(Field Injection) ]**

필드 주입(Field Injection)은 필드에 바로 의존 관계를 주입하는 방법이다. IntelliJ에서 필드 인젝션을 사용하면 Field injection is not recommended이라는 경고 문구가 발생한다.

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;
    @Autowired
    private MemberService memberService;

}
```

필드 주입을 이용하면 코드가 간결해져서 과거에 상당히 많이 이용되었던 주입 방법이다. 하지만 필드 주입은 외부에서 접근이 불가능하다는 단점이 존재하는데, 테스트 코드의 중요성이 부각됨에 따라 필드의 객체를 수정할 수 없는 필드 주입은 거의 사용되지 않게 되었다. 또한 필드 주입은 반드시 DI 프레임워크가 존재해야 하므로 반드시 사용을 지양해야 한다. 그렇기에 애플리케이션의 실제 코드와 무관한 테스트 코드나 설정을 위해 불가피한 경우에만 이용하도록 하자.

---

## 의존성 주입의 장점

- 코드의 재사용성, 유연성이 높아진다. 하나의 작업만 수행하는 작은 객체는 많은 상황에서 재결합하고 재사용하기가 쉽기 때문이다.
- 객체간 결합도가 낮기 때문에 한 클래스를 수정했을 때 다른 클래스도 수정해야 하는 상황을 막아준다
- 유지보수가 쉬우며 테스트가 용이해진다
- 확장성을 가진다

## 의존성 주입의 단점

- 책임이 분리되어 있기 때문에 클래스 수를 늘림으로써 복잡성이 증가한다.
- 주입된 객체들에 관한 코드 추적이 어렵다.
- 초기 개발 노력이 필요하다.
- 의존성 주입 프레임워크를 사용하면 빌드 시간이 늘어날 수 있으며, 프레임워크에 대한 의존도를 높인다.