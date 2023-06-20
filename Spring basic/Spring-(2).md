# [스프링 기본] Section 2.스프링 핵심 원리 이해1

---

김영한- 스프링 핵심 원리 - 기본편 강의를 듣고 정리한 내용

- 제목
    - [[스프링 기본] Section 1. 객체지향 설계와 스프링](https://www.notion.so/Section-1-803144bd18b048d9b38291c294d30be8?pvs=21)
    - **[[스프링 기본] Section 2.스프링 핵심 원리 이해1](https://www.notion.so/Section-2-1-c8e4a1567c6f46b39dbbc62c59b4f2fd?pvs=21)**

---

**비즈니스 요구사항과 설계**

- **회원**
    - 회원을 가입하고 조회할 수 있다.
    - 회원은 일반과 VIP 두 가지 등급이 있다.
    - 회원 데이터는 자체 DB를 구축할 수 있고, 외부 시스템과 연동할 수 있다. (미확정)
- 주문과 할인 정책
    - 회원은 상품을 주문할 수 있다.
    - 회원 등급에 따라 할인 정책을 적용할 수 있다
    - 할인 정책은 모든 VIP는 1000원을 할인해주는 고정 금액 할인을 적용해달라(변경 가능성 있다.)
    - 할인 정책은 변경 가능성이 높다. 회사의 기본 할인 정책을 아직 정하지 못했고, 오픈 직전 까지 고민을 미루고 싶다. 최악의 경우 할인을 적용하지 않을 수도 있다.(미확정)
    
           →인터페이스와 구현체간에 얼마나 자연스러운 연계가 되는지 확인하는 과정
    

---

## 회원 도메인 설계

- 회원 도메인 요구사항
    - 회원을 가입하고 조회할 수 있다.
    - 회원은 일반과 VIP 두 가지 등급이 있다.
    - 회원 데이터는 자체 DB를 구축할 수 있고, 외부 시스템과 연동할 수 있다.(미확정)

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/07aabc89-416a-4d79-87fb-4e9bbd348145">

회원 서비스 역할은 MemberService라는 interface가 되고 MemberServiceImpl에서 해당 역할을 구현한다.
⇒ 이처럼 구현체가 하나인경우 접미사로 Impl을 붙혀준다.

회원 저장소 역할은 MemberRepository라는 interface가 되고 이를 구현하는 방법은 DB 회원저장소를 구현하는 DbMemberRepository나 기 타외부 저장소 API가 될 수 있지만 미확정이고 아직 구현할 수 없기에 테스트를 위한 MemoryMemberRepository에서 구현한다.

---

## ****회원 도메인 개발****

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/4486536d-4a01-4bc7-8321-348bcefbbf1e">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/902a4ff6-ab63-49c6-80ee-11fea00938d1">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/587dc914-211a-4c80-b996-c06e3522e24f">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/2a53ca68-3c85-40e8-a908-98575b3709cd">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/af2d7fe1-a1da-479c-bf0b-72272203afe5">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/a1eef530-c594-45b0-b05d-093a21a3d91b">

## 회원 테스트 코드

MemberApp을 만들어서 테스트도 하였지만, JUnit 테스트를 활용하는게 더 올바른 방법이다.

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/c131fe23-d102-4160-bba1-9b6890378c67">

---

## 주문과 할인 도메인 설계

- 주문과 할인 정책
    - 회원은 상품을 주문할 수 있다.
    - 회원 등급에 따라 할인 정책을 적용할 수 있다
    - 할인 정책은 모든 VIP는 1000원을 할인해주는 고정 금액 할인을 적용해달라(나중에 변경 될 수 있다.)
    - 할인 정책은 변경 가능성이 높다. 회사의 기본 할인 정책을 아직 정하지 못했고, 오픈 직전까지 고민을 미루고 싶다. 최악의 경우 할인을 적용하지 않을 수 도 있다.(미확정)

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/8850a6ac-bafc-461c-8884-83cabf0dc5e5">

클라이언트는 시작과 동시에 주문서비스에 주문생성을 요구한다 (회원 ID, 상품명, 상품가격) 그리고 주문서비스는 회원등급을 확인하기 위해 먼저 회원 저장소에 회원을 조회하고, 등급에 따라서 할인 정책에 위임한다. 마지막으로는 DB에 주문을 저장하는게 아닌 주문 결과를 return 한다.

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/362e1ce2-e9da-4af2-9948-685b4d6d8076">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/f690ed70-af80-433a-9ed8-18aa23a41820">

이 객체 다이어그램은 DB가 바뀔수도 있다는것을 나타낸 것이다.

---

## 주문가 할인 도메인 개발

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/4b8a1d00-a63e-4eff-9770-eecf83eb1a4b">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/3b52d3d7-af57-4764-bb8e-616f9f13204a">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/46b2530b-51ce-413d-88f3-d27937bace98">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/f6e217dc-6026-4750-9a49-7f81f15cef07">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/bbd12b44-0f88-4267-91cf-45f902f926f5">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/be90e8a1-a96a-4189-a6e1-97b07fd00670">