# 제어의 역전(Inversion of Control, IoC)

---

일반적으로, main() 같은 프로그램이 시작되는 지점에서 다음에 사용할 오브젝트를 결정, 생성하고, 만들어진 오브젝트 내의 메소드를 호출하는 작업을 반복한다. 이런 구조에서 각 오브젝트느 프로그램 흐름을 결정하거나 사용할 오브젝트를 구성하는 작업에 능동적으로 참여한다.

**즉, 모든 종류의 작업을 사용하는 쪽에서 제어하는 구조이다.**

이에 반하여, 제어의 역전은 구현 객체가 직접 코드를 호출하는 것(스스로 프로그램의 코드를 제어)이 아니라, 프레임 워크가 **외부에서 코드를 대신 호출, 관리해 주는 것**을 의미한다. 말 그대로 제어 권이 넘어가기 때문에 제어의 역전 (Inversion of Control) 이라고 한다.

<aside>
💡 IoC 정리

- 작업을 수행하는 쪽에서 Object 를 생성하는 제어 흐름의 개념을 거꾸로 뒤집는다.
- IoC 에서는 Object 가 자신이 사용할 Object 를 생성하거나 선택하지 않는다.
- Object 는 자신이 어떻게 생성되고 어떻게 사용되는지 알 수 없다.
- 모든 Object 는 제어 권한을 위임받는 특별한 Object 에 의해서 만들어 지고 사용된다.
</aside>

---

## **IoC 구현 방법**

- **DL (Dependency Lookup) - 의존성 검색** 저장소에 저장되어 있는 빈(Bean)에 접근하기 위하여 개발자들이 컨테이너에서 제공하는 API 를 이용하여  사용하고자 하는 빈(Bean) 을 Lookup 하는 것
- **DI (Dependency Injection) - 의존성 주입** 각 계층 사이, 각 클래스 사이에 필요로 하는 의존 관계를 컨테이너가 자동으로 연결해주는것 각 클래스 사이의 의존 관계를 빈 설정(Bean Definition) 정보를 바탕으로 컨테이너가 자동으로 연결해 주는 것 DL 사용 시 컨테이너 종속성이 증가하여, 이를 줄이기 위하여 DI를 사용

---

## **IoC 용어 정리**

**bean - 스프링에서 제어권을 가지고 직접 만들고 관계를 부여하는 오브젝트**

자바빈, EJB의 빈과 비슷한 오브젝트 단위의 애플리케이션 컴포넌트를 말한다. 하지만 스프링을 사용하는 애플리케이션에서 만들어지는 모든 오브젝트가 빈은아니다. 스프링의 빈은 스프링 컨테이너가 생성과 관계설정, 사용등을제어해주는 오브젝트를 가리킨다.

**bean factory - 스프링의 IoC를 담당하는 핵심컨테이너**

빈을 등록/생성/조회/반환/관리한다. 보통은 bean factory 를 바로 사용하지 않고 이를 확장한 application context 를 이용한다. BeanFactory 는 bean factory 가 구현하는 interface 이다. (getBean()등의 메소드가 정의되어 있음)

**application context - bean factory를 확장한 IoC 컨테이너**

빈의 등록/생성/조회/반환/관리의 기능은 bean factory 와 같지만, 여기에 spring의 각종 부가 서비스를 추가로 제공한다. ApplicationContext는 application context가 구현해야 하는 interface이여, BeanFactory 를 상속한다.

**configuration metadata (설정정보/설정 메타정보)**

**application context 혹은 bean factory 가 IoC를 적용하기 위해 사용하는 메타정보**

스프링의 설정정보는 컨테이너에 어떤 기능을 세팅하거나 조정하는 경우에도 사용하지만 주로 bean 을 생성/구성하는 용도로 사용된다.

**container (IoC container)**

**IoC 방식으로 bean을 관리한다는 의미에서 bean factory 나 application context 를 가리킨다. (spring container = application context)** application context는 그 자체로 ApplicationContext 인터페이스를 구현한 오브젝트를 가리키기도 하는데, 하나의 애플리케이션에 보통 여러개의 ApplicationContext Object가 만들어진다. 이를 통칭해서 strping container라고 부를 수 있다.

간단하게, 객체를 관리하는 컨테이너이다, 컨테이너에 객체를 담아 두고, 필요할 때에 컨테이너로부터 객체를 가져와 사용할 수 있도록 하고 있다.

---

## IoC 호출방식

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/49aced66-f245-4cac-ae97-ba67a92c6b87">