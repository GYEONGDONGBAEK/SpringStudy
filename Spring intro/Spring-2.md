# [스프링 입문] Section 2. 스프링 웹 개발 기초

---

김영한-스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술 강의를 듣고 정리한 내용

<details>
<summary>제목</summary>
<div>

[스프링 입문] Section 1. 프로젝트 환경설정

[스프링 입문] Section 2. 스프링 웹 개발 기초

</div>
</details>
    

---

# 스프링 웹 개발 기초

- 정적 컨텐츠
- MVC와 템플릿 엔진
- API

---

# 정적 컨텐츠

파일 그대로를 반환시켜 보여준다 → 따로 프로그래밍 할 수 없다.

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/e5f0b5a1-28b8-454f-a8c8-f502bab142cb">

### 정적 컨텐츠 동작

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/07cefb2a-561d-4281-b173-8240bd075c5c">

1. 웹 브라우저가 내장 톰캣서버에게 요청을 함.
2. 내장 톰캣 서버가 스프링 컨테이너에 hello-static과 관련된 컨트롤러가 있는지 찾아본다.
3. 매핑(mapping)된 컨트롤러가 없다면 resources 폴더에 관련된 파일이 있는지 찾아본다.
4. 파일을 찾았다면, 웹 브라우저에 반환한다.

---

# MVC와 템플릿 엔진

MVC: Model, View, Controller 의 약자로 애플리케이션을 세 가지 역할로 구분한 개발 방법론

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/11330a89-ac84-417e-aa5c-a5e4881c0371">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/78534be6-5702-4497-ad30-a602e7eb8a15">

### MVC, 템플릿 엔진 동작

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/137176f3-5d64-4343-8002-6e7a7006d70d">

1. 웹 브라우저가 내장 톰캣서버에게 요청을 함.
2. 내장 톰캣서버가 요청을 스프링 컨테이너에게 넘긴다.
3. 관련된 Controller가 리턴을 hello-template로 하면서 model에 key값은 name ,value값은 spring으로 ViewResolver로 보냄
4. ViewResolver가 View를 찾고 템플릿 엔진과 연결
5. 템플릿 엔진이 HTML을 변환한 후 웹 브라우저에게 반환한다.

---

# API

spring 개발 시 이야기하는 API방식은, JSON 형식으로 바꾸어 반환하는 것을 말한다.

view없이 그대로 http body에 전달하는 방식이다.

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/ab48d685-147d-4b12-938d-50dbccc55c6d">

- @ResponseBody 를 사용하면 ViewResolver를 사용하지 않음
- HTTP의 BODY에 문자 내용을 직접 반환

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/69e81ac3-b93a-4cc4-ac2d-470bb9f5384f">

### @ResponseBody 사용원리

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/caf92c7a-94c3-479d-a349-54d2be06a0e4">

- @ResponseBody 를 사용
    - HTTP의 BODY에 문자 내용을 직접 반환
    - viewResolver 대신에 HttpMessageConverter 가 동작
    - 기본 문자처리: StringHttpMessageConverter
    - 기본 객체처리: MappingJackson2HttpMessageConverter
    - byte 처리 등등 기타 여러 HttpMessageConverter가 기본으로 등록되어 있음
        
        

### 동작

1. 웹 브라우저가 내장 톰캣서버에게 요청을 함.
2. 내장 톰캣서버가 요청을 스프링 컨테이너에게 넘긴다.
3. @ResponseBody 라는 annotation이 붙어 있으면 HTTP 에 데이터를 그대로 반환해야 한다고 생각함
4. 데이터가 문자이면 HttpMessageConveter 의 StringConverter가 동작한다.
5.  HTTP 응답에 데이터를 넣어서 그대로 반환한다.
- 만약 데이터가 문자가 아닌 객체라면 ? JSON 방식으로 HTTP 응답에 반환하겠다.
    1. HttpMessageConveter 의 JsonConverter가 동작한다.
    2. HTTP 응답에 데이터를 JSON 형식으로 반환한다.