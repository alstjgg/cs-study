# HTTP 프로토콜이란?

HTTP(Hypertext Transfer Protoco)는 텍스트 기반의 통신 규약으로, 인터넷 상에서 데이터를 주고 받기 위해 서버/클라이언트 모델을 따르는 프로토콜이다. 

## 동작 방식

앞서 언급했듯이, HTTP는 서버/클라이언트 모델을 따른다. 클라이언트에서 요청을 보내면 서버는 요청을 처리해서 응답한다. 이때, HTML 문서 외에도 JSON 데이터나 XML 데이터 등의 정보를 주고 받을 수 있다. 참고로, 초기 HTTP는 오직 HTML 문서를 주고 받기 위해서 설계되었다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F66fb8c7d-9e4b-486a-aeb8-877cb1b72b62%2FUntitled.png?table=block&id=659c3962-7d9f-4030-8abb-3285bbc871a4&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### 클라이언트

서버에 요청하는 클라이언트 소프트웨어(IE, Chrome, Firefox, ...)가 설치된 컴퓨터를 이용한다. 클라이언트는 URI를 이용해서 서버에 접속하고 데이터를 요청할 수 있다.

### 서버

클라이언트의 요청을 받아서 요청을 해석하고 응답을 하는 소프트웨어(Apache, nginx, IIS, ...)가 설치된 컴퓨터를 이용한다. 웹 서버는 보통 표준 포트인 80번 포트로 서비스를 한다.

### 예시로 알아보는 HTTP

클라이언트 프로그램에서 사용자가 회원 가입을 요청하게 되면, 서버로 회원 정보를 보내게 되고 서버는 회원 정보를 저장한 후 잘 저장했다고 클라이언트에 응답한다. 이 과정에서 클라이언트와 서버 간의 교류가 HTTP라는 규약을 이용한다.

## Request (요청)

클라이언트가 서버에게 연락하는 것을 요청이라고 하며, 요청을 보낼 때는 요청에 대한 정보를 담아 서버로 보낸다.

### 예시로 알아보는 Request

서버는 일종의 주문서를 받아 클라이언트가 어떤 것을 원하는지 파악할 수 있다. 여기서 요청은 주문서를 작성하는 것과 유사하다.

### Request Method (요청의 종류)

- Get: 자료를 요청할 때 사용
- Post: 자료의 생성을 요청할 때 사용
- Put: 자료의 모든 부분에 대해 수정을 요청할 때 사용
- Patch: 자료의 일부분에 대해 수정을 요청할 때 사용
- Delete: 자료의 삭제를 요청할 때 사용
- Options: preflight 요청할 때 사용 (CORS와 관련이 있음.)

### Request HTTP 메시지 예시

```java
GET https://www.test.com HTTP/1.1								                // 시작 줄
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) ...			  // 헤더
Upgrade-Insecure-Requests: 1

// 본문 - 현재는 없음.
```

**[시작 줄]**

첫 줄은 시작 줄로 메소드 종류, URI, HTTP 버전으로 구성되어 있다.

- GET : HTTP Method
- https://test.com : 사이트 주소
- HTTP/1.1 : HTTP 버전

**[헤더]**

두 번째 줄부터는 헤더이며 요청에 대한 정보를 담고 있다. User-Agent, Upgrade-Insecure-Requests 등 많은 헤더가 있다.

**[본문] - 헤더에서 한 줄 띄고**

본문은 요청을 할 때 함께 보내는 데이터를 담는 부분이다. 현재 예시에는 단순히 주소로 Get 요청만 보내고 있고, 따로 데이터를 담아 보내지는 않아서 본문이 비어 있다.

## Response (응답)

서버가 요청에 대한 답변을 클라이언트에게 보내는 것을 응답이라고 한다.

### Status Code (상태 코드)

상태 코드는 많은 종류가 이으며, 모두 숫자 세 자리로 이루어져 있다. 아래와 같이 크게 다섯 부류로 나눌 수 있다.

- 1XX (조건부 응답): 요청을 받았으며 작업을 계속한다.
- 2XX (성공): 클라이언트가 요청한 동작을 수신하여 이해했고, 성공적으로 처리했다.
- 3XX (리다이렉션 완료): 클라이언트는 요청을 마치기 위해 추가 동작을 취해야 한다.
- 4XX (요청 오류): 클라이언트에 오류가 있음을 나타낸다.
- 5XX (서버 오류): 서버가 유효한 요청을 명백하게 수행하지 못했음을 나타낸다.

### Response HTTP 메시지 예시

```java
HTTP/1.1 200 OK														                          // 시작 줄
Connection: keep-alive									                            // 헤더
Content-Encoding: gzip												 
Content-Length: 35653
Content-Type: text/html;

<!DOCTYPE html><html lang="ko" data-reactroot=""><head><title...    // 본문
```

**시작 줄**

첫 줄은 HTTP 버전, 상태 코드, 상태 메시지로 구성되어 있다. 200은 성공적인 요청이었다는 뜻이다.

**헤더**

두 번째 줄부터는 헤더로, 응답에 대한 정보를 담고 있다.

**본문**

응답에는 대부분의 경우 본문이 있다. 보통 데이터를 요청하고 응답 메시지에는 요청한 데이터를 담아서 보내기 때문이다. 현재 응답 메시지에는 HTML이 담겨 있는데, 이 HTML을 받아 브라우저가 화면에 렌더링할 수 있다.

## HTTP의 특징

- TCP/IP를 위에서 동작하는 응용 프로토콜이다.
- 연결 상태를 유지하지 않는 비연결성 프로토콜이다.
- 상태가 없는 무상태 프로토콜이다.
- 요청/응답 방식으로 동작한다.

### Connectionless

HTTP는 Connectionlss 방식으로 작동하는데, 이 방식은 클라이언트와 서버가 한 번 연결을 맺은 후, 클라이언트 요청에 대해 서버가 응답을 마치면 맺었던 연결을 끊어 버리는 특징을 갖고 있다. 기본적으로 자원 하나에 대해서 하나의 연결을 만드는데, Connectionless 작동 방식은 아래의 장단점을 갖는다.

**장점**

- 불특정 다수를 대상으로 하는 서비스에 적합한 방식이다.
- 수십 만 명이 웹 서비스를 사용하더라도 접속 유지는 최소한으로 할 수 있기 때문에 더 많은 유저의 요청을 처리할 수 있다.

**단점**

- 연결을 끊어버리기 때문에 클라이언트의 이전 상태를 알 수가 없는데, 이러한 HTTP의 특징을 Stateless라고 한다.
- 클라이언트의 이전 상태 정보를 알 수 없게 되면, 웹 서비스를 하는데 여러 문제가 생긴다. 예를 들어 클라이언트가 과거에 로그인을 성공하더라도 로그 정보를 유지할 수가 없다.

### Stateless

위에서 언급했듯이 비연결적인 특성으로 연결이 해제됨과 동시에 서버와 클라이언트가 이전에 요청한 결과에 대해서 잊어버리게 된다. 즉, 클라이언트가 이전 요청과 같은 데이터를 원한다고 하더라도 다시 서버에 연결을 하여 동일한 요청을 시도해야 한다. HTTP는 이러한 무상태 특성으로 인해 독립적인 쌍의 요청과 응답을 처리하기 때문에, 단순하고 상태를 저장해야 하는 서버의 부담을 줄일 수 있다.

하지만, 상태가 서버에 꼭 저장되어야만 하는 '인증'을 HTTP는 어떻게 구현을 할까? 매번 클라이언트는 데이터베이스를 왕복하면서 인증을 해야 하는 것일까? 바로, 쿠키를 통해 인증을 구현할 수 있다.

**쿠키 (Cookie)**

쿠키는 웹 브라우저가 보관하는 데이터다. 웹 서버는 쿠키를 생성하여 웹 브라우저에 정보를 전송할 수 있고, 쿠키는 key-value 형태로 웹 브라우저의 쿠키 저장소에 저장된다. 서버로부터 쿠키를 전달 받은 웹 브라우저는 이후 웹 서버에 요청을 보낼 때 쿠키를 헤더에 실어서 함께 전송한다. 이를 이용하면 웹 서버와 클라이언트는 필요한 값을 공유하고 상태를 유지할 수 있다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fbae5019d-dea3-4868-bf84-9a45425dc78a%2FUntitled.png?table=block&id=f71eab72-a6a1-4526-b3ee-00be8871c212&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

하지만 쿠키는 네트워크를 통해 전달 되기 때문에 중간에 쿠키를 탈취할 수 있다는 취약점이 있다. 쿠키에 바로 아이디와 비밀번호 값이 들어 있으면 위험하기 때문에 이를 보완하고자 세션이나 토큰이라는 방식을 도입할 수 있다.

**세션 (Session)**

세션도 마찬가지로 클라이언트의 상태를 저장할 수 있다. 쿠키와 다른 점은, 쿠키는 각 브라우저의 별 도 쿠키 저장소에 저장되는 반면 세션은 서버에 저장이 된다.

웹 브라우저는 각각 별도의 세션을 갖는다. 각 세션을 구분하기 위해 고유 ID를 할당하고, 웹 서버는 각 브라우저에게 세션 ID를 전송한다. 웹 브라우저는 웹 서버에 연결 시 매번 세션 ID를 보내서 웹 서버가 어떤 세션을 사용할 지 알 수 있도록 한다. 이때, 웹 서버와 웹 브라우저가 세션 ID를 주고 받기 위해 사용하는 것이 쿠키다. 세션 ID를 쿠키에 넣는다고 생각하면 된다.

쿠키와 세션을 종합하여 로그인이 이루어지는 과정을 살펴 보자. 우리는 로그인 정보를 웹 컨테이너 메모리의 세션 객체에 저장하게 되고, 클라이언트는 세션 ID를 전송하여 브라우저 고유 세션을 식별하게 된다. 이 후 세션 객체 내부에 로그인 여부를 확인할 수 있는 특정 속성이 존재하면 로그인한 것으로 판단할 수 있다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fda13c820-5cd8-41ed-a638-71b50fbd2e6a%2FUntitled.png?table=block&id=ce3e9392-1504-4127-b068-178b98553120&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

**토큰 (Token)**

토큰 기반 인증의 방법으로 많은 웹 서버들은 JWT (JSON Web Token)을 사용한다. 토큰 기반 인증 방식은 유저의 정보가 서버에 저장되지 않는 무상태라는 특징이 있다. Flow는 다음과 같다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F48762422-a6ee-4628-92b0-c10884651226%2FUntitled.png?table=block&id=7360ce91-0011-4a91-bef4-4524a7648a67&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 유저가 로그인을 하고 서버에 세션을 이용해서 정보를 기록하는 대신, 토큰을 발급한다.
- 클라이언트는 발급된 토큰을 Local Storage에 저장하거나, 토큰을 쿠키에 담아서 쿠키 저장소에 저장한다.
- 클라이언트는 요청 시 저장된 토큰을 Authorization 헤더에 포함하여 보낸다.
- 서버는 매 요청 시 클라이언트로부터 전달 받은 헤더의 토큰 정보를 검증한다.

## 출처

[https://shlee0882.tistory.com/107](https://shlee0882.tistory.com/107)

[https://velog.io/@surim014/HTTP란-무엇인가](https://velog.io/@surim014/HTTP%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)

[https://tansfil.tistory.com/58](https://tansfil.tistory.com/58)

[https://hyuntaeknote.tistory.com/m/3](https://hyuntaeknote.tistory.com/m/3)

## 예상 면접 질문 및 답변

### HTTP란?

HTTP(Hyper Text Transfer Protoco)는 텍스트 기반의 통신 규약으로, 인터넷 상에서 데이터를 주고 받기 위해 서버/클라이언트 모델을 따르는 프로토콜이다.

### Request Method를 아는 대로 설명해 보시오.

- Get: 자료를 요청할 때 사용
- Post: 자료의 생성을 요청할 때 사용
- Put: 자료의 모든 부분에 대해 수정을 요청할 때 사용
- Patch: 자료의 일부분에 대해 수정을 요청할 때 사용
- Delete: 자료의 삭제를 요청할 때 사용
- Options: preflight 요청할 때 사용 (CORS와 관련이 있음.)

### HTTP의 특징은?

- TCP/IP를 위에서 동작하는 응용 프로토콜이다.
- 연결 상태를 유지하지 않는 비연결성 프로토콜이다.
- 상태가 없는 무상태 프로토콜이다.
- 요청/응답 방식으로 동작한다.

### HTTP의 특성 중비연결성 프로토콜이 무엇인가?

클라이언트와 서버가 한 번 연결을 맺은 후, 클라이언트 요청에 대해 서버가 응답을 마치면 맺었던 연결을 끊어 버리는 프로토콜이다. 수십 만 명이 웹 서비스를 사용하더라도 접속 유지는 최소한으로 할 수 있기 때문에 더 많은 유저의 요청을 처리할 수 있지만, 클라이언트의 이전 상태를 알 수 없어서 Stateless하다는 특징이 있다.

### HTTP의 특성 중 무상태가 무엇인가?

비연결적인 특성으로 연결이 해제됨과 동시에 서버와 클라이언트가 이전에 요청한 결과에 대해서 잊어버리게 된다. 즉, 클라이언트가 이전 요청과 같은 데이터를 원한다고 하더라도 다시 서버에 연결을 하여 동일한 요청을 시도해야 한다. HTTP는 이러한 무상태 특성으로 인해 독립적인 쌍의 요청과 응답을 처리하기 때문에, 단순하고 상태를 저장해야 하는 서버의 부담을 줄일 수 있다.

### HTTP는 무상태인데 로그인과 같은 인증을 어떻게 구현하는가?

최초의 로그인 요청에서 응답으로 받은 아이디와 비밀 번호를 쿠키에 담으면 이전 결과를 저장해 둘 수 있다.

### 쿠키에 바로 아이디와 비밀 번호를 담으면 탈취될 시 위험하지 않은가?

그래서 쿠키에 아이디와 비밀 번호를 바로 담기보다는 세션이나 토큰을 담아서 보안을 강화한다.

(이후 세션, 토큰 질문이 나올텐데 인증 주제가 아니므로 자세한 내용은 생략.)

# HTTPS

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F701c1e31-8e7a-44a0-b70e-45566b2eea74%2FUntitled.png?table=block&id=ad32aff1-2485-43b4-bf53-985d3149a342&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

HTTPS는 Hypertext Transfer Protocol Secure의 약자로 보안이 강화된 HTTP 프로토콜이다. HTTP 프로토콜만으로 통신을 수행하면, 다음과 같이 평문 데이터가 모두 노출이 된다. 즉, 암호화되지 않은 데이터를 전송하기 때문에 서버와 클라이언트가 주고 받는 메시지를 외부에서 볼 수 있기 때문에 보안에 취약하다. 이러한 취약점을 보완하고자 만들어진 것이 바로 HTTPS다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fc3dda29a-728b-4155-af11-3111dcc7a5ce%2FUntitled.png?table=block&id=bc54e408-c741-4780-8c3e-fedd33256ce0&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

와이어 샤크를 이용하면 위와 같이 HTTP 패킷을 확인할 수 있는데, 유저의 민감한 데이터가 그대로 노출되는 것을 확인할 수 있다.

## HTTPS의 동작 과정

HTTPS는 SSL이라는 보안 프로토콜 위에서 동작하는 HTTP다. 그렇다면, SSL과 TLS가 무엇일까?

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F4c42a919-df0b-4e8a-9937-c49460fa0216%2FUntitled.png?table=block&id=9ed2d997-37b7-49a0-bd10-02f9bf5fc8c2&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

SSL은 Secure Socket Layer의 약어로, 응용 계층과 전송 계층 사이에서 동작하는 보안 소켓 계층을 뜻한다. 인터넷 상에서 데이터를 안전하게 전송하기 위한 인터넷 암호화 통신 프로토콜이라고 생각하면 된다. SSL은 전자상거래 등의 보안을 위해 넷스케이프 사에서 처음 개발하였고, 시간이 지나 TLS로 표준화하였다.

## 출처

[https://coding-start.tistory.com/208](https://coding-start.tistory.com/208)

## 예상 면접 질문 및 답변

### HTTPS란?

HTTPS는 Hypertext Transfer Protocol Secure의 약자로 보안이 강화된 HTTP 프로토콜이다. HTTP 프로토콜만으로 통신을 수행하면, 다음과 같이 평문 데이터가 모두 노출이 된다. 즉, 암호화되지 않은 데이터를 전송하기 때문에 서버와 클라이언트가 주고 받는 메시지를 외부에서 볼 수 있기 때문에 보안에 취약하다. 이러한 취약점을 보완하고자 만들어진 것이 바로 HTTPS다.

### HTTPS의 동작 방식을 설명하라.

추후 주제에서 설명.
