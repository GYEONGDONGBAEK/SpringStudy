# [스프링 기본] Section 3. 객체지향 원리 적용-(2)

---

김영한- 스프링 핵심 원리 - 기본편 강의를 듣고 정리한 내용

- 제목
    - [[스프링 기본] Section 1. 객체지향 설계와 스프링](https://www.notion.so/Section-1-803144bd18b048d9b38291c294d30be8?pvs=21)
    - **[[스프링 기본] Section 2.스프링 핵심 원리 이해1](https://www.notion.so/Section-2-1-c8e4a1567c6f46b39dbbc62c59b4f2fd?pvs=21)**
    - [[스프링 기본] Section 3. 객체지향 원리 적용-(1)](https://www.notion.so/Section-3-1-163408c7a48c4e09a5db38ab73d9fb60?pvs=21)
    - [[스프링 기본] Section 3. 객체지향 원리 적용-(2)](https://www.notion.so/Section-3-2-2081960991f942fdbfe31675c3be1abc?pvs=21)

---

## 새로운 구조와 할인 정책 적용 

정액 할인 정책에서 정률 할인 정책으로 변경하자!

→ 구성을 담당하고 있는 AppConfig 코드만 변경하면 된다 ! 

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/4b99edcb-8a46-43b6-b985-6ef532efc7e1">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/517fe3fd-6777-4fbc-a549-c69f2d2044cb">

---

## 좋은 객체 지향 설계의 원칙 적용

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/a9217d6c-ca04-4d99-be29-9bdaf0735da5">
---

## 스프링으로 전환하기

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/80a4ef36-d39d-4d1f-b834-d27e53c2d420">

### MemberApp에 적용

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/ed0ced21-6b0f-494b-804c-a95f513fadfa">

### OrderApp에 적용

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/7870e16a-72aa-435f-882e-8e247f052ca1">

### OrderApp에 적용 결과

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/9d97eb51-434a-416c-ae6c-7efc0b209ba0">

---

## 스프링 컨테이너

- ApplicationContext 를 스프링 컨테이너라 한다.
- 기존에는 개발자가 AppConfig 를 사용해서 직접 객체를 생성하고 DI를 했지만, 이제부터는 스프링 컨테이너를 통해서 사용한다.
- 스프링 컨테이너는 @Configuration 이 붙은 AppConfig 를 설정(구성) 정보로 사용한다. 여기서 @Bean이라 적힌 메서드를 모두 호출해서 반환된 객체를 스프링 컨테이너에 등록한다. 이렇게 스프링 컨테이너에 등록된 객체를 스프링 빈이라 한다.
- 스프링 빈은 @Bean 이 붙은 메서드의 명을 스프링 빈의 이름으로 사용한다.
- 이전에는 개발자가 필요한 객체를 AppConfig 를 사용해서 직접 조회했지만, 이제부터는 스프링 컨테이너를 통해서 필요한 스프링 빈(객체)를 찾아야 한다.
- 스프링 빈은 applicationContext.getBean() 메서드를 사용해서 찾을 수 있다.
