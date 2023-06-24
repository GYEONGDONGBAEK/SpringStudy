# [스프링 기본] Section 4. 스프링 컨테이너와 스프링 빈

---

김영한- 스프링 핵심 원리 - 기본편 강의를 듣고 정리한 내용

- 제목
    - [[스프링 기본] Section 1. 객체지향 설계와 스프링](https://www.notion.so/Section-1-803144bd18b048d9b38291c294d30be8?pvs=21)
    - **[[스프링 기본] Section 2.스프링 핵심 원리 이해1](https://www.notion.so/Section-2-1-c8e4a1567c6f46b39dbbc62c59b4f2fd?pvs=21)**
    - [[스프링 기본] Section 3. 객체지향 원리 적용-(1)](https://www.notion.so/Section-3-1-163408c7a48c4e09a5db38ab73d9fb60?pvs=21)
    - [[스프링 기본] Section 3. 객체지향 원리 적용-(2)](https://www.notion.so/Section-3-2-2081960991f942fdbfe31675c3be1abc?pvs=21)
    - [[스프링 기본] Section 4. 스프링 컨테이너와 스프링 빈](https://www.notion.so/Section-4-47da930a3c5d464eb4787b0e2cc6b574?pvs=21)

---

## 스프링 컨테이너 생성

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/28702428-4d3c-4b9e-8a1e-69233798dfe1">

## 생성 과정

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/cb770db9-8cef-4e8a-9fe3-6b10a8925a14">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/6bd44105-d2e5-4b26-ba22-62eaa171d1b5">

---

## 컨테이너에 등록된 모든 빈 조회

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/9b0c1c44-b225-43a9-b8fb-cf4d848a6128">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/054fae47-e3e9-4545-a429-c943bdd83b14">

## 스프링 컨테이너에서 스프링 빈을 찾는 가장 기본적인 조회 방법

- ac.getBean(빈이름, 타입)
- ac.getBean(타입)
- 조회 대상 스프링 빈이 없으면 예외 발생
NoSuchBeanDefinitionException: No bean named 'xxxxx' available

## 스프링 빈 조회- 동일한 타입이 둘 이상

- 타입으로 조회시 같은 타입의 스프링 빈이 둘 이상이면 오류가 발생한다. 이때는 빈 이름을 지정하자
- ac.getBeansOfType()을 사용하면 해당 타입의 모든 빈을 조회할 수 있다.

---

## 스프링 빈 조회- 상속관계

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/5a489c42-9b53-4d7f-9663-98ce4d4268b9">

---

## BeanFactory와 ApplicationContext

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/0958054f-12a7-4db8-b7ba-1005245398ad">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/36277af0-4fdb-4810-a2be-68892449196c">

- 정리
    - ApplicationContext는 BeanFactory의 기능을 상속받는다.
    - ApplicationContext는 빈 관리기능 + 편리한 부가 기능을 제공한다.
    - BeanFactory를 직접 사용할 일은 거의 없다. 부가기능이 포함된 ApplicationContext를 사용한다.
    - BeanFactory나 ApplicationContext를 스프링 컨테이너라 한다.

---

## 다양한 설정 형식 지원- 자바코드, XML

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/3990b215-cc97-4ffc-b150-f1d8bc3776d4">

- Annotation 기반 자바코드 설정 사용
    - new AnnotationConfigApplicationContext(AppConfig.class)
    - AnnotationConfigApplicationContext 클래스를 사용하면서 자바코드로 된 설정 정보를 넘기면 된다.
- XML 설정 사용
    - GenericXmlApplicationContext를 사용하면서 xml 설정파일을 넘기면 된다.

### XML 기반의 빈 설정

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/3633c293-bc8f-487a-bd7f-70a737b9f3df">

---

## 스프링 빈 설정 메타 정보- BeanDefinition

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/1e5f78d7-fdfc-4152-a3fd-8b8861e7ce58">

### BeanDefinition에 대해

- BeanDefinition 정보
    - BeanClassName: 생성할 빈의 클래스 명
    - factoryBeanName: 팩토리 역할의 빈을 사용할 경우 이름, 예) appConfig
    - factoryMethodName: 빈을 생성할 팩토리 메서드 지정, 예) memberService
    - Scope: 싱글톤(기본값)
    - lazyInit: 스프링 컨테이너를 생성할 때 빈을 생성하는 것이 아니라, 실제 빈을 사용할 때 까지 최대한 생성을 지연처리 하는지 여부
    - InitMethodName: 빈을 생성하고, 의존관계를 적용한 뒤에 호출되는 초기화 메서드 명
    - DestroyMethodName: 빈의 생명주기가 끝나서 제거하기 직전에 호출되는 메서드 명
    - Constructor arguments, Properties: 의존관계 주입에서 사용한다.