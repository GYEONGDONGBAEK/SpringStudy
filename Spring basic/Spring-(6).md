# [스프링 기본] Serction 5. 싱글톤 컨테이너

---

김영한- 스프링 핵심 원리 - 기본편 강의를 듣고 정리한 내용

- 제목
    - [[스프링 기본] Section 1. 객체지향 설계와 스프링](https://www.notion.so/Section-1-803144bd18b048d9b38291c294d30be8?pvs=21)
    - **[[스프링 기본] Section 2.스프링 핵심 원리 이해1](https://www.notion.so/Section-2-1-c8e4a1567c6f46b39dbbc62c59b4f2fd?pvs=21)**
    - [[스프링 기본] Section 3. 객체지향 원리 적용-(1)](https://www.notion.so/Section-3-1-163408c7a48c4e09a5db38ab73d9fb60?pvs=21)
    - [[스프링 기본] Section 3. 객체지향 원리 적용-(2)](https://www.notion.so/Section-3-2-2081960991f942fdbfe31675c3be1abc?pvs=21)
    - [[스프링 기본] Section 4. 스프링 컨테이너와 스프링 빈](https://www.notion.so/Section-4-47da930a3c5d464eb4787b0e2cc6b574?pvs=21)
    - [[스프링 기본] Section 5. 싱글톤 컨테이너](https://www.notion.so/Serction-5-9b602ff180ce46c2906c66930d7ab20c?pvs=21)

---

## 웹 애플리케이션과 싱글톤

### 순수한 DI 컨테이너

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/218f884e-5517-4834-9342-e2ea0b7460d0">

싱글톤 없는 순수한 DI 컨테이너는 요청을 할때마다 새로운 객체를 생성한다.

고객 트래픽이 초당 100이 발생한다면 초당 100개의 객체가 생성되고 소멸된다

→ 메모리 낭비가 심하다.

  ⇒해결은 객체가 1개만 생성되고 공유 하도록 설계하면 된다 → 싱글톤 패턴

---

### 싱글톤 패턴

- 클래스의 인스턴스가 1개만 생성되는 것을 보장하는 디자인 패턴
- 객체 인스턴스를 2개 이상 생성하지 못하도록 막아야 한다. → private 생성자를 이용

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/8b940b09-a373-4fff-9675-0498d6569fb0">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/bdac3cbe-1712-4eec-a2d3-01e0d6137710">

### 싱글톤 패턴의 문제점

- 싱글톤 패턴을 구현하는 코드 자체가 많이 들어간다.
- 의존 관계상 클라이언트가 구체 클래스에 의존한다 ⇒DIP를 위반한다.
- 클라이언트가 구체 클래스에 의존해서 OCP 원칙을 위반할 가능성이 높다.
- 테스트 하기 어렵다
- 내부 속성을 변경하거나 초기화 하기 어렵다.
- private 생성자로 자식 클래스를 만들기 어렵다.
- 유연성이 떨어진다.
- 안티 패턴으로 불리기도 한다.

---

## 싱글톤 컨테이너

스프링 컨테이너는 싱글톤 패턴의 문제점을 해결하며 객체 인스턴스를 싱글톤으로 관리한다.

    → 스프링 빈이 싱글톤으로 관리되는 빈이다.

- 스프링 컨테이너는 싱글톤 패턴을 적용하지 않아도 객체를 싱글톤으로 관리한다.
- 스프링 컨테이너는 싱글톤 컨테이너 역할을 한다. 이렇게 싱글톤 객체를 생성하고 관리하는 기능을 싱글톤 레지스트리라 한다.
- 싱글톤 패턴을 위한 지저분한 코드가 들어가지 않아도 된다.
- DIP,OCP,테스트,private 생성자로 부터 자유롭게 싱글톤을 이용할 수 있다.

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/2ba76c45-0cf8-49eb-8ef7-22f9fb79a21d">
---

## 싱글톤 방식의 주의점

- 싱글톤 패턴이든, 스프링 같은 싱글톤 컨테이너를 사용하든, 객체 인스턴스를 하나만 생성해서 공유하는 싱글톤 방식은 여러 클라이언트가 하나의 같은 객체 인스턴스를 공유하기 때문에 싱글톤 객체는 상태를 유지(stateful)하게 설계하면 안된다.
- 무상태(stateless)로 설계해야 한다

   →특정 클라이언트에 의존적인 필드가 있으면 안된다.

   →특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안된다.

   →가급적 읽기만 가능해야 한다.

   →필드 대신에 자바에서 공유되지 않는 ,지역변수, 파라미터, ThreadLocal 등을 사용해야 한다.

- 스프링 빈의 필드에 공유 값을 설정하면 정말 큰 장애가 발생할 수 있다.

---

## @Configuration 과 싱글톤

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/57f06e47-a7b2-4fc6-afa1-9b265abb1725">

memberService 빈을 만드는 코드가 memberRepository()를 호출

  ⇒ new MemoryMemberRepository() 를 호출함

orderService 빈을 만드는 코드도 동일하게 memberRepository()를 호출

  ⇒ new MemoryMemberRepository() 를 호출함

자바코드로만 봤을때 각각 다른 객체를 가지는 MemoryMemberRepository를 호출해 싱글톤이 깨지는것 처럼 보이지만 MemoryMemberRepository 객체는 모두 공유되어 사용된다.

---

## @Configuration 과 바이트코드 조작의 마법

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/5e128b10-ea9f-4691-8007-18521f232eb4">
스프링이 CGLIB 라는 바이트코드 조작 라이브러리를 사용해 AppConfig 클래스를 상속받는 임의의 다른 클래스를 만들고, 그 다른 클래스를 스프링 빈으로 등록해 싱글톤 패턴을 유지시킨다.

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/928b06fb-fdd3-418b-8485-7eaae7287a5c">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/a745e636-b344-438f-b32a-c2a09b79f0b2">

### @Configuration 없이 @Bean만 사용한다면 ?

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/3e8c8ba4-35a0-407d-8aab-f387af9e36b4">

- @Bean만 사용해도 스프링 빈으로 등록 되지만, 싱글톤을 보장하지는 않는다.

⇒memberRepository()처럼 의존관계 주입이 필요해서 메서드를 직접 호출할 때 싱글톤을 보장                  하지 않는다.

⇒@AutoWired로 해결이 가능하다. 하지만 @Configuration을 사용하도록 하자.