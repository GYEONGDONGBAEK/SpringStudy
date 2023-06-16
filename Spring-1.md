# [스프링 입문] Section 1. 프로젝트 환경설정

---

김영한-스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술 강의를 듣고 정리한 내용

- 목록
    
    [스프링 입문] Section 1. 프로젝트 환경설정
    
    [스프링 입문] Section 2. 스프링 웹 개발 기초

---

# 스프링 부트란?

Java Spring Framework(Spring Framework)는 Java Virtual Machine(JVM)에서 실행되는 프로덕션급 독립형 애플리케이션을 만들기 위한 인기 있는 엔터프라이즈급 오픈소스 프레임워크.

Java Spring Boot(Spring Boot)는 세 가지 핵심 기능을 통해 Spring Framework를 사용하여 더 빠르고 쉽게 웹 애플리케이션과 마이크로서비스를 개발하도록 돕는 툴.

1. 자동 구성
2. 구성에 대한 독선적 접근 방식
3. 독립형 애플리케이션을 만드는 능력

이러한 특징들이 함께 작용하여 최소한의 구성 및 설정으로 Spring 기반 애플리케이션을 설정하도록 지원하는 툴을 제공해줌으로서 기존에 어려운 초기설정에 쏟아야 했을 시간과 노력을 절약하여 비즈니스 로직을 구현하는데 개발자들의 시간과 노력을 집중하게 할 수 있었음.

또한 스프링 부트는 자체적인 웹 서버를 내장하고 있어, 빠르고 간편하게 배포를 진행 할 수 있다는 장점도 있음.

---

# 프로젝트 생성

- 스프링 부트 스타터 사이트로 이동해서 스프링 프로젝트 생성

     →[Spring Initializr](https://start.spring.io) 를 이용

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/ff5b566e-5340-4600-afee-7c38eb019e61">

(1) Project : 빌드 툴을 선택.

(2) Language : 프로젝트에 사용하고자 하는 프로그래밍 언어를 선택.

(3) Spring Boot : 스프링 부트의 버전을 선택합니다. 버전 이름 뒤에 아무 것도 붙지 않은 버전 중, 가장 최신 버전을 선택.

- SNAPSHOT : 아직 개발이 완료되지 않은 버전을 의미.
- M(Milestone) : 개발은 완료되었으나, 아직 기능들을 개선하는 중 또는 버그를 수정하고 있는 버전을 의미.
- RC(Release Candidate) : 기능 개선과 버그 수정이 완료되었으나, 최종적으로 릴리즈되지는 않은 버전을 의미.

(4) Project Metadata : 프로젝트의 정보를 입력합니다.

- Group : 프로젝트를 만드는 그룹의 이름으로, 보통 기업의 도메인 명을 역순으로 입력.
- Artifact : 빌드 결과물의 이름.
- Name : 프로젝트의 이름.
- Description : 프로젝트에 대한 간략한 설명을 입력.
- Package name : 프로젝트에 생성할 패키지를 설정.
- Packaging : 배포를 위해 프로젝트를 압축하는 방법을 선택.
- Java : 여러분의 PC에 설치된 JDK의 버전을 선택.

(5) Dependencies : 프로젝트를 통해 만들 애플리케이션의 동작에 필요한 라이브러리들을 선택.

---

# 라이브러리

Gradle은 의존관계가 있는 라이브러리를 함께 다운로드 한다.

**스프링 부트 라이브러리**

- Spring-boot-starter-web
    - Spring-boot-starter-tomcat: 톰캣( 웹서버)
    - Spring-webmvc: 스프링 웹 MVC
- Spring-boot-starter-thymeleaf:타임리프 템플릿 엔진(View)
- Spring-boot-starter(공통): 스프링부트 + 스프링 코어+ 로깅
    - Spring-boot
        - Spring-core
    - Spring-boot-starter-logging
        - logback(사용빈도가 더 높음), slf4j

**테스트 라이브러리**

- Spring-boot-starter-test
    - junit: 테스트 프레임워크
    - mockito: 목 라이브러리
    - assertj: 테스트 코드를 좀 더 편하게 작성하게 도와주는 라이브러리
    - spring-test: 스프링 통합 테스트 지원

---

# View 환경설정

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/87bee3f8-f27a-4b84-af81-d46ce00f245e">

- 스프링 부트가 제공하는 Welcome Page 기능
    
    static/index.html 을 올려두면 Welcome page 기능을 제공한다.
    

---

## thymeleaf 템플릿 엔진

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/13ea5fc7-4e3b-4854-9ea8-be8d13cab314">

### thymeleaf 동작

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/5d920313-20c2-464b-888f-e6cf0e8c3fe2">