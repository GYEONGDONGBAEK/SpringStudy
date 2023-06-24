# [스프링 기본] Section 3. 객체지향 원리 적용-(1)

---

김영한- 스프링 핵심 원리 - 기본편 강의를 듣고 정리한 내용

- 제목
    - [[스프링 기본] Section 1. 객체지향 설계와 스프링](https://www.notion.so/Section-1-803144bd18b048d9b38291c294d30be8?pvs=21)
    - [[스프링 기본] Section 2.스프링 핵심 원리 이해1](https://www.notion.so/Section-2-1-c8e4a1567c6f46b39dbbc62c59b4f2fd?pvs=21)
    - [[스프링 기본] Section 3. 객체지향 원리 적용-(1)](https://www.notion.so/Section-3-1-163408c7a48c4e09a5db38ab73d9fb60?pvs=21)
    - [[스프링 기본] Section 3. 객체지향 원리 적용-(2)](https://www.notion.so/Section-3-2-2081960991f942fdbfe31675c3be1abc?pvs=21)

---

# AppConfig

### 정책 변화가 생겼을때?

RateDiscountPolicy를 추가했을때?

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/7b4b4533-1af6-427f-a551-09a1b71c8766">

DiscountPolicy를 구현하는 RateDiscountPolicy를 만들면 된다 !

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/84bb6a56-9e84-4cde-abac-fec1812f3fcf">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/9fb96e35-8a02-45b1-8023-fb55ee5ad575">

RateDiscountPolicy를 만들어 준 뒤 OrderServiceImpl을 고치면 된다!

→ 문제점 발생

역할과 구현은 충실하게 분리했고, 다형성 활용, 인터페이스와 구현 객체를 분리 했지만

SOLID 중 OCP,DIP 를 준수하지 못했다.

DIP: OrderServiceImpl 은 DiscountPolicy 인터페이스에 의존하지만 구현클래스인 FixDiscountPolicy,

RateDiscountPolicy 에도 의존하고있다.

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/68b35a59-b026-4cd3-8ac5-b4ccaed11450">

OCP: 클라이언트인 OrderServiceImpl의 코드까지 고쳐야 한다 → OCP 위반

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/bd4869d6-d1ad-4e10-b5b2-069ec50a7074">

## 문제 해결

OrderServiceImpl이 인터페이스에만 의존하도록 설계를 변경

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/37a84bac-a2a8-4768-9f9f-ba54f9dfb85e">

- 문제점: 구현 객체가 없는데 어떻게 코드를 실행할 수 있을까?
    
    다른 누군가가 OrderServiceImpl에 DiscountPolicy의 구현 객체를 주입시켜야 한다 !
    
    → AppConfig 클래스를 만들어 동작에 필요한 구현 객체를 만들고
    
    →객체 인스턴스의 참조를 생성자를 통해서 주입해준다( Dependency Injection)
    

---

## AppConfig 생성

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/19239d33-84ac-4aa6-868f-1674a1a5dc6c">

## 생성자 주입

### MeberService 생성자 주입

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/c27f614a-a55e-4c84-8993-550ee357c50a">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/6b7f5329-181a-4591-9bd4-c3e7cf232cf7">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/5aba2f39-1b76-43bc-804e-510a5cc32217">

### OrderService 생성자 주입

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/394c67fe-cf3b-4625-81e6-887b3e7d9ab0">

---

# AppConfig 리팩터링

리팩터링 전

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/fd4bb4bc-95c1-4192-b892-5353a848f1bc">

- 문제점
    
    MemoryMemberRespository가 중복으로 선언되었다.
    
    역할에 따른 구현이 잘 드러나있지 않다.
    

리팩터링 후

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/6a91bf8e-9015-4293-947b-8f8bf4d7959b">
