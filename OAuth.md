# OAuth(Open Authorization)

---

# **OAuth 란?**

OAuth는 인터넷 사용자들이 비밀번호를 제공하지 않고 다른 웹사이트 상의 자신들의 정보에 대해 웹사이트나 애플리케이션의 접근 권한을 부여할 수 있는 공통적인 수단으로서 사용되는, 접근 위임을 위한 개방형 표준이다.

쉽게 말하자면, 우리의 서비스가 우리 서비스를 이용하는 유저의 타사 플랫폼 정보에 접근하기 위해서 권한을 타사 플랫폼으로부터 위임 받는 것 이다.

---

# OAuth 2.0 역할

| Resource Owner | 리소스 소유자. 본인의 정보에 접근할 수 있는 자격을 승인하는 주체. 예시로 구글 로그인을 할 사용자를 말한다. 
Resource Owner는 클라이언트를 인증(Authorize)하는 역할을 수행하고, 인증이 완료되면 동의를 통해 권한 획득 자격(Authorization Grant)을 클라이언트에게 부여한다. |
| --- | --- |
| Client | Resource Owner의 리소스를 사용하고자 접근 요청을 하는 어플리케이션 |
| Resource Server | Resource Owner의 정보가 저장되어 있는 서버 |
| Authorization Server | 권한 서버입니다. 인증/인가를 수행하는 서버로 클라이언트의 접근 자격을 확인하고 Access Token을 발급하여 권한을 부여하는 역할을 수행. |

---

# OAuth 2.0의 주요용어

| Authentication (인증) | 인증, 접근 자격이 있는지 검증하는 단계 |
| --- | --- |
| Authorization (인가) | 자원에 접글할 권한을 부여하고 리소스 접근 권한이 담긴 Access Token을 제공 |
| Access Token | 리소스 서버에게서 리소스 소유자의 정보를 획득할 때 사용되는 만료 기간이 있는 Token |
| Refresh Token | Access Token 만료시 이를 재발급 받기위한 용도로 사용하는 Token |

---

# ****OAuth 2.0의 권한 부여 방법****

## **1. Authorization Code Grant │ 권한 부여 승인 코드 방식**

**권한 부여 승인을 위해 자체 생성한 Authorization Code를 전달하는 방식으로 많이 쓰이고 기본이 되는 방식이다.** 간편 로그인 기능에서 사용되는 방식으로 클라이언트가 사용자를 대신하여 특정 자원에 접근을 요청할 때 사용되는 방식이다. 보통 타사의 클라이언트에게 보호된 자원을 제공하기 위한 인증에 사용됩니다. Refresh Token의 사용이 가능한 방식이다.

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/05e89bdd-2ac9-47d5-92df-1cca3efb6322">

1. 권한 부여 코드 요청 시, response_type=code로 요청하게 되면 클라이언트는 권한 서버*(Authorization Server)*에서 제공하는 로그인 페이지를 브라우저에 띄워 출력한다.
2. 해당 페이지를 통해 사용자가 로그인을 하면 Authorization Server는 권한 부여 코드 요청 시 전달받은 redirect_uri로 Authorization Code를 전달한다.
3. Client는 전달받은 Authorization Code를 Authorization Server의 API를 통해 Access Token으로 교환하게 된다.

※

**Access Token을 획득하는 과정에서 Authorization Code 발급 과정이 들어간 이유?**

만약 Authorization Code를 발급받는 과정이 생략되고 Access Token을 바로 발급받는다면, Authorization Server는 Access Token을 전달하기 위한 방법으로 권한 부여 코드 요청 시 전달받은 redirect_uri을 사용할 수밖에 없다.

Redirect URI을 통해 데이터를 전달하는 방법은 URI 자체에 데이터를 실어 전달하는 방법 밖에 없으며, 이 방법을 사용하면 중요한 데이터인 Access Token이 브라우저를 통해 바로 노출되게 된다.

이러한 문제를 보완하기 위해 Authorization Code 발급 과정이 추가된 것이며, redirect uri로 전달된 Authorization Code는 프런트엔드에서 백엔드로 전달되고, 백엔드는 전달받은 Authorization Code를 가지고 Authorization Server의 API에 요청하여 Access Token을 발급받는다.

이 과정을 통해 백엔드 사이에서 access token이 전달되기 때문에 액세스 토큰의 탈취 위험이 줄어드는 등, 다른 방식에 비해 보안적으로 안전하다는 특징이 있다.

---

## 2. **Implicit Grant / 암묵적 승인 방식**

자격 증명을 안전하게 저장하기 힘든 클라이언트 사이드에서의 OAuth2 인증에 최적화된 방식으로 response_type=token의 형식으로 요청된다.

암묵적 승인 방식은 권한 부여 코드*(Authorization Code)* 발급 과정 없이 바로 액세스 토큰이 발급된다.

access token이 uri를 통해 바로 전달되기 때문에 만료 기간을 짧게 설정해야 한다는 특징이 있다.

해당 방식은 refresh token의 사용이 불가능한 방식이며, 이 방식에서 Authorization Server는 client_secret을 사용해 클라이언트를 인증하지 않는다.

access token을 획득하기 위한 절차가 간소화되기 때문에 응답성과 효율성은 높아지지만, access token이 uri로 전달되는 보안적인 측면에서의 단점이 있다.

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/b574bc26-573c-43fd-9520-73c09c55b6a2">

1. 권한 부여 승인 요청 시 response_type을 token으로 설정하여 요청하면, 클라이언트는 권한 서버에서 제공하는 로그인 페이지를 브라우저에 띄워 출력한다.
2. 해당 페이지를 통해 사용자가 로그인을 하면 Authorization Server는 권한 부여 승인 요청 시 전달받은 redirect_uri로 Authorization Code가 아닌 access token을 전달하게 된다.

---

## **3. Resource Owner Password Credentials Grant │ 자원 소유자 자격증명 승인 방식**

**간단하게 username, password로 `Access Token`을 받는 방식이다.**

클라이언트가 타사의 외부 프로그램일 경우에는 이 방식을 적용하면 안됩니다. 자신의 서비스에서 제공하는 어플리케이션일 경우에만 사용되는 인증 방식입니다. `Refresh Token`의 사용도 가능하다.

username, password를 통해 바로 access token을 발급받는 간단한 로직이다.

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/9ffec841-32ac-4a22-9be3-b308fcc2f957">

---

## **4. Client Credentials Grant │클라이언트 자격증명 승인 방식**

****

**클라이언트의 자격증명만으로 Access Token을 획득하는 방식이**다.

OAuth2의 권한 부여 방식 중 가장 간단한 방식으로 클라이언트 자신이 관리하는 리소스 혹은 권한 서버에 해당 클라이언트를 위한 제한된 리소스 접근 권한이 설정되어 있는 경우 사용된다.

이 방식은 자격증명을 안전하게 보관할 수 있는 클라이언트에서만 사용되어야 하며, Refresh Token은 사용할 수 없다.

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/05b3af44-779f-4a32-b5d7-5f4b48b6201b">