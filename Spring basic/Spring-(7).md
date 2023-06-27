# [스프링 기본] Section 6. 컴포넌트 스캔

김영한- 스프링 핵심 원리 - 기본편 강의를 듣고 정리한 내용

---

- 제목
    - [[스프링 기본] Section 1. 객체지향 설계와 스프링](https://www.notion.so/Section-1-803144bd18b048d9b38291c294d30be8?pvs=21)
    - **[[스프링 기본] Section 2.스프링 핵심 원리 이해1](https://www.notion.so/Section-2-1-c8e4a1567c6f46b39dbbc62c59b4f2fd?pvs=21)**
    - [[스프링 기본] Section 3. 객체지향 원리 적용-(1)](https://www.notion.so/Section-3-1-163408c7a48c4e09a5db38ab73d9fb60?pvs=21)
    - [[스프링 기본] Section 3. 객체지향 원리 적용-(2)](https://www.notion.so/Section-3-2-2081960991f942fdbfe31675c3be1abc?pvs=21)
    - [[스프링 기본] Section 4. 스프링 컨테이너와 스프링 빈](https://www.notion.so/Section-4-47da930a3c5d464eb4787b0e2cc6b574?pvs=21)
    - [[스프링 기본] Section 5. 싱글톤 컨테이너](https://www.notion.so/Section-5-9b602ff180ce46c2906c66930d7ab20c?pvs=21)
    - [[스프링 기본] Section 6. 컴포넌트 스캔](https://www.notion.so/Section-6-83d5136cc14241ddaef17698eaa5cf26?pvs=21)

---

## 컴포넌트 스캔과 의존관계 자동 주입 시작하기

컴포넌트 스캔을 사용하려면 @ComponentScan 을 설정정보에 붙여주면 된다.

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/c31ab78a-461b-43ca-bcdc-66ab01a49dc2">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/f038ee76-41af-4ece-8ba3-785dd95a7c4b">

---

### 탐색 위치와 기본 스캔대상

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/b110f6bf-95c3-40b1-8782-064a2778e596">

하지만 basePackage를 쓰지 않고 설정 정보 클래스의 위치를 프로젝트 제일 최상단에 두고 @ComponentScan 붙이면 프로젝트에 존재하는 모든 패키지들이 컴포너느 스캔의 대상이 된다.

---

### 컴포넌트 스캔 기본 대상

- @Component: 컴포넌트 스캔에 사용
- @Controller : 스프링 MVC 컨트롤러에서 사용
- @Service: 스프링 비즈니스 로직에서 사용
- @Repository: 스프링 데이터 접근 계층에서 사용
- @Configuration : 스프링 설정 정보에서 사용

---

### 필터

- includeFilters : 컴포넌트 스캔 대상을 추가로 지정한다.
- excludeFilters: 컴포넌트 스캔에서 제외할 대상을 지정한다.

⇒ @Component 면 충분하기 때문에, includeFilters 를 사용할 일은 거의 없다. excludeFilters는 가끔 사용하지만 사용 빈도가 높지 않다.

---

## 중복 등록과 충돌

### 자동 빈 등록 vs 자동 빈 등록

컴포넌트 스캔에 의해 자동으로 스프링 빈이 등록되는데, 그 이름이 같은 경우 스프링은 오류를 발생시킨다.

→ConfilctingBeanDefinitionException 예외 발생

### 수동 빈 등록 vs 자동 빈 등록

수동 빈 등록이 우선권을 가진다.(수동 빈이 자동 빈을 오버라이딩 해버린다)

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/76eeb3ad-c7d4-4145-8251-c135dbc4c273">
최근 스프링 부트에서는 수동 빈 등록과 자동 빈 등록이 충돌나면 오류가 발생하도록 기본 값을 바꾸었다.