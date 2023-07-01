# [스프링 기본] Section 9. 빈 스코프

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
    - [[스프링 기본] Section 9. 빈 스코프](https://www.notion.so/Section-9-f990a97679344e88840d56e3fc6f30d2?pvs=21)

---

# 빈 스코프?

스프링 빈이 스프링 컨테이너의 시작과 함께 생성되어서 스프링 컨테이너가 종료될 때 까지 유지된다. 이것은 스프링 빈이 기본적으로 싱글톤 스코프로 생성되기 때문이다. 스코프는 번역 그대로 빈이 존재할 수 있는 범위를 뜻한다

### 스프링이 지원하는 다양한 스코프

- 싱글톤: 기본 스코프, 스프링 컨테이너의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프.
- 프로토 타입: 스프링 컨테이너는 프로토타입 빈의 생성과 의존관계 주입까지만 관여하고 더는 관리하지 않는 매우 짧은 범위의 스코프이다.
- 웹 관련 스코프

      request: 웹 요청이 들어오고 나갈때 까지 유지되는 스코프

      session: 웹 세션이 생성되고 종료될 때 까지 유지되는 스코프

      application: 웹의 서블릿 컨텍스트와 같은 범위로 유지되는 스코프

---

## 프로토 타입 스코프

스프링 컨테이너는 프로토타입 빈을 생성하고, 의존관계 주입, 초기화까지만 처리한다.

### 싱글톤 빈 요청

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/0b198514-b722-4c8b-83dc-bb0b4c68eab1">

1. 싱글톤 스코프의 빈을 스프링 컨테이너에 요청한다.
2. 스프링 컨테이너는 본인이 관리하는 스프링 빈을 반환한다.
3. 이후에 스프링 컨테이너에 같은 요청이 와도 같은 객체 인스턴스의 스프링 빈을 반환한다.

### 프로토타입 빈 요청 1

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/8171238b-4e34-4c97-80e0-29e98459f590">

1. 프로토타입 스코프의 빈을 스프링 컨테이너에 요청한다.
2. 스프링 컨테이너는 이 시점에 프로토타입 빈을 생성하고, 필요한 의존관계를 주입한다.

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/5fb5e620-a99b-4e11-adb1-937ca5e5558d">

1. 스프링 컨테이너는 생성한 프로토타입 빈을 클라이언트에 반환한다.
2. 이후에 스프링 컨테이너에 같은 요청이 오면 항상 새로운 프로토타입 빈을 생성해서 반환한다.

---

### 싱글톤과 프로토타입 빈

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/30eb310b-f5f7-487e-b7be-19b39da81f7f">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/574635c2-2266-45c3-8b84-596c511a9c8b">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/2c4e8055-df44-4497-96cb-d247771b1769">

---

### Provider

만약 사용할때마다 새로운 프로토타입 빈을 생성하기 위해서는 Provider를 쓰면 된다.

---

### 웹 스코프

- 웹 스코프의 특징
    - 웹 스코프는 웹 환경에서만 동작한다
    - 웹 스코프는 프로토타입과 다르게 스프링이 해당 스코프의 종료시점까지 관리한다. 따라서 종료 메서드가 호출된다.
- 웹 스코프 종류
    - request: HTTP 요청 하나가 들어오고 나갈 때 까지 유지되는 스코프, 각각의 HTTP 요청마다 별도의 빈 인스턴스가 생성되고, 관리된다.
    - session: HTTP Session과 동일한 생명주기를 가지는 스코프
    - application: 서블릿 컨텍스트( ServletContext )와 동일한 생명주기를 가지는 스코프
    - websocket: 웹 소켓과 동일한 생명주기를 가지는 스코프

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/b845db5f-91dd-4f9c-8d4b-858cf3345295">

---

### 스코프와 프록시

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/48b4b41c-d9c9-45fe-b061-84bf38061117">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/87f0b7a4-d869-4576-b60f-9191702ce1cd">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/ac8fbd18-a2ea-4603-9f38-08b60f1c9077">