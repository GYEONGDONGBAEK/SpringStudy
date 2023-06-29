# [스프링 기본] Section 7. 의존관계 자동 주입

---

- 제목
    - [[스프링 기본] Section 1. 객체지향 설계와 스프링](https://www.notion.so/Section-1-803144bd18b048d9b38291c294d30be8?pvs=21)
    - **[[스프링 기본] Section 2.스프링 핵심 원리 이해1](https://www.notion.so/Section-2-1-c8e4a1567c6f46b39dbbc62c59b4f2fd?pvs=21)**
    - [[스프링 기본] Section 3. 객체지향 원리 적용-(1)](https://www.notion.so/Section-3-1-163408c7a48c4e09a5db38ab73d9fb60?pvs=21)
    - [[스프링 기본] Section 3. 객체지향 원리 적용-(2)](https://www.notion.so/Section-3-2-2081960991f942fdbfe31675c3be1abc?pvs=21)
    - [[스프링 기본] Section 4. 스프링 컨테이너와 스프링 빈](https://www.notion.so/Section-4-47da930a3c5d464eb4787b0e2cc6b574?pvs=21)
    - [[스프링 기본] Section 5. 싱글톤 컨테이너](https://www.notion.so/Section-5-9b602ff180ce46c2906c66930d7ab20c?pvs=21)
    - [[스프링 기본] Section 6. 컴포넌트 스캔](https://www.notion.so/Section-6-83d5136cc14241ddaef17698eaa5cf26?pvs=21)
    - [[스프링 기본] Section 7. 의존관계 자동 주입](https://www.notion.so/Section-7-00c640bf1d8d446ea04ba16bbc19b1a2?pvs=21)

---

# 다양한 의존관계 주입 방법

의존 관계 주입은 크게 4가지 방법이 있다.

- 생성자 주입
- 수정자 주입(setter 주입)
- 필드 주입
- 일반 메서드 주입

### 생성자 주입

- 생성자를 통해서 의존관계를 주입 받는 방법
- 생성자 호출시점에 딱 1번만 호출되는 것이 보장된다.
- 불변, 필수 의존관계에서 사용

### 수정자(setter) 주입

- setter라 불리는 필드의 값을 변경하는 수정자 메서드를 통해서 의존관계를 주입하는 방법
- 선택,변경 가능성이 있는 의존관계에 사용
- 자바 빈 프로퍼티 규약의 수정자 메서드 방식을 사용하는 방법

### 필드 주입

- 필드에 바로 주입하는 방법
- 코드가 간결하지만, 외부에서 변경이 불가능해 테스트 하기 어렵다.
- DI 프레임 워크가 없으면 아무것도 할 수 없다.
- 설정을 목적으로 하는 @Configuration 에서 특별한 용도로 사용

      ⇒거의 사용하지 않는다.

### 일반 메서드 주입

- 일반 메서드를 통해서 주입 받는다.
- 한번에 여러 필드를 주입 받을 수 있다.
- 일반적으로 잘 사용하지 않는다.

---

## 옵션 처리

- @Autowired(required=false) : 자동 주입할 대상이 없으면 수정자 메서드 자체가 호출이 안됨
- org.springframework.lang.@Nullable: 자동 주입할 대상이 없다면 null을 반환한다.
- Optional<>: 자동 주입할 대상이 없으면 Optional.empty가 입력된다.

---

## 생성자 주입을 선택하는 이유

### 불변

- 대부분의 의존관계 주입은 한번 일어나면 애플리케이션 종료시점까지 의존관계를 변경할 일이 없다. 오히려 대부분의 의존관계는 불변이여야 한다.
- 수정자 주입을 사용한다면 , set메서드를 public 으로 놓아야 하기때문에 변할 가능성이 있다.
- 반면에 생성자 주입은 객체를 생성할때 딱 1번만 호출되므로 이후에 호출되는 일이 없다. 따라서 불변성을 가지게 설계 할 수 있다.

### 누락

- 생성자 주입을 사용하면 주입 데이터를 누락 했을 때 **컴파일 오류**가 발생한다.

### final 키워드

- 생성자 주입을 사용하면 필드에 final 키워드를 사용할 수 있다. 혹시 생성자에 값이 설정되지 않았다면, 컴파일 시점에서 오류가 난다.

---

# 롬복

- 롬복 라이브러리가 제공하는 @RequiredArgsConstructor 기능을 사용하면 final이 붙은 필드를 모아서 생성자를 자동으로 만들어준다.
- 최신 트렌드는 생성자를 딱 1개 두고 @Autowired를 생략하는 방법을 사용한다. 여기에 롬복의@RequiredArgsConstructor 를 함께 사용하면 기능은 다 제공하면서, 깔끔한 코드를 사용할 수 있다.

### 롬복 전

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/07127bd0-7d38-4fea-8668-cb05de37bbe0">

### 롬복 후

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/fba9b419-9a11-4272-881f-7c814c4cd95b">

---

## @Autowired 필드 명, @Qualifier , @Primary

@Autowired 는 타입으로 조회한다.

만약 선택된 빈이 2개 이상이라면 NoUniqueBeanDefinitionException 오류가 발생한다.

이를 해결하기 위해 @Autowired 필드 명, @Qualifier , @Primary 를 쓴다.

### @Autowired 필드 명

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/5e5ff84e-fce5-40ca-9b30-c31f12235934">

@Autowired 매칭 정리

1. 타입 매칭
2. 타입 매칭의 결과가 2개 이상일 때 필드 명, 파라미터 명으로 빈 이름 매칭

### @Qualifier

추가 구분자를 붙여주는 방법. 주입시 추가적인 방법을 제공하는 것이지 빈 이름을 변경하는 것이 아니다.

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/c30cb45e-6f80-47ee-9fee-85ee3e6857e5">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/038481ca-8111-48dc-bd24-03305acc4d19">

@Qualifier 정리

1. @Qualifier끼리 매칭
2. 빈 이름 매칭

### @Primary

우선 순위를 정하는 방법. @Autowired시에 여러개의 빈이 매칭 되면 @Primary가 우선권을 가진다.

---

## 조회한 빈이 모두 필요할 때, List, Map

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/24954c13-c795-4930-ab5d-9547218389bd">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/34faa216-3bce-422b-bfee-074b001c0366">