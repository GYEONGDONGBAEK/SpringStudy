# [스프링 기본] Section 8. 빈(Bean) 생명주기 콜백

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
    - [[스프링 기본] Section 8. 빈(Bean) 생명주기 콜백](https://www.notion.so/Section-8-Bean-66075391a65f40dc9ad4b856f263d202?pvs=21)

---

# 빈 생명주기 콜백 시작

데이터베이스 커넥션 풀이나 , 네트워크 소켓처럼 애플리케이션 시작시점에 필요한 연결을 미리 해두고 , 애플리케이션 종료 시점에 연결을 모두 종료하는 작업을 진행하려면, 객채의 초기화와 종료 작업이 필요하다.

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/c74a90ef-1b5f-4f12-8950-9788d8a98a85">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/2ce7edbd-2ad1-4cdc-9f1d-124c74ae6f26">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/42558cbf-abff-4724-9f3f-a02114beb20c">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/db928a62-d11d-4134-a40b-16c32cf01ea3">

생성자 부분을 보면 url 정보 없이 connect가 호출되는 것을 확인할 수 있다.
너무 당연한 이야기이지만 객체를 생성하는 단계에는 url이 없고, 객체를 생성한 다음에 외부에서 수정자 주입을 통해서 setUrl() 이 호출되어야 url이 존재하게 된다.

스프링은 **객체 생성 ⇒  의존관계주입** 의 간단한 라이프 사이클을 가진다

스프링 빈은 객체를 생성하고, 의존관계 주입이 끝난 다음에야 데이터를 사용할 준비가 완료된다. 따라서 초기화 작업은 의존관계 주입이 모두 완료되고 난 다음에 호출해야 한다.

그래서 스프링은 의존관계 주입이 완료되면 스프링 빈에게 콜백 메서드를 통해 초기화 시점을 알려준다. 또한 종료 직전에 소멸 콜백을 제공한다.

### 스프링 빈의 이벤트 라이프사이클

스프링 컨테이너 생성 ⇒ 스프링 빈 생성 ⇒ 의존관계 주입 ⇒ 초기화 콜백 ⇒ 사용 ⇒ 소멸전 콜백 ⇒스프링 종료

---

### 스프링의 3가지 빈 생명주기 콜백

- 인터페이스(InitializingBean, DisposableBean)
- 설정 정보에 초기화 메서드, 종료 메서드 지정
- @PostConstruct, @PreDestroy 애노테이션 지원

## 인터페이스 InitializingBean, DisposableBean

- InitializingBean 은 afterPropertiesSet() 메서드로 초기화를 지원한다.
- DisposableBean 은 destroy() 메서드로 소멸을 지원한다.

### 단점

- 이 인터페이스들은 스프링 전용 인터페이스다. 해당 코드가 스프링 인터페이스에 의존한다.
- 초기화 , 소멸 메서드의 이름을 변경할 수 없다.
- 내가 코드를 고칠 수 없는 외부라이브러리를 사용한다면 이 방법은 적용할 수 없다.

인터페이스를 사용하는 초기화 방법은 스프링 초기에 나온 방법들이고, 현재는 잘 사용하지 않는다.

## 빈 등록 초기화, 소멸 메서드 지정

설정 정보에 @Bean(initMethod = "init", destroyMethod = "close") 처럼 초기화,소멸 메서드를 지정할 수 있다.

### 특징

- 메서드 이름을 자유롭게 할 수 있다.
- 스프링 빈이 스프링코드에 의존하지 않는다.
- 코드가 아니라 설정 정보를 사용하기 때문에 코드를 고칠수 없는 외부 라이브러리에도 초기화, 종료 메서드를 적용할 수 있다.

### 종료 메서드 추론

@Bean의 destroyMethod 의 defalut값은 (inferred)(추론) 으로 등록되어 있다.

라이브러리의 대부분이 close,shutdown 이라는 이름으로 종료메서드를 사용한다. 이 추론 기능은 close,shutdown 이라는 이름의 메서드를 자동으로 호출한다. 즉, 종료 메서드를 추론해서 호출한다.

이 기능을 사용하기 싫다면 destroyMethod="" 처럼 빈 공백을 지정하면 된다.

## 애노테이션 @PostConstruct, @PreDestroy

### 특징

- 최신 스프링에서 가장 권장하는 방법이다.
- 애노테이션 하나만 붙이면 되므로 매우 편리하다.
- 패키지를 잘 보면 javax.annotation.PostConstruct 이다. 스프링에 종속적인 기술이 아니라 JSR-250 라는 자바 표준이다. 따라서 스프링이 아닌 다른 컨테이너에서도 동작한다.
- 컴포넌트 스캔과 잘 어울린다.
- 유일한 단점은 외부 라이브러리에는 적용하지 못한다는 것이다. 외부 라이브러리를 초기화, 종료 해야 하면 @Bean의 기능을 사용하자.