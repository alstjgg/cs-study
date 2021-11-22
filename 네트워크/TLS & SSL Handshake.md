## SSL Handshake란?

Handshake는 악수를 의미하는데, 통신을 하는 브라우저와 웹 서버가 서로 암호화 통신을 시작할 수 있도록 신분을 확인하고 필요한 정보를 클라이언트와 서버가 주거니 받거니 하는 과정이 악수와 비슷하여 붙여진 이름이다.

## 대칭 키와 비대칭 키

### 대칭 키 (비밀 키)

하나의 키로 데이터를 암호화하고 복호화한다. 하나의 키로 암호화 및 복호화를 하기 때문에 해당 키가 노출된다면 보안 상 아주 치명적인 문제가 발생한다. 다만, 암호화 및 복호화에 드는 비용이 적다는 장점이 있다.

대칭 키 방식은 암호를 주고 받는 사람들 사이에 대칭 키를 전달하는 것이 어렵다. 왜냐하면 위에서 언급했듯이 대칭 키가 유출되면 키를 획득한 공격자는 암호의 내용을 복호화 할 수 있기 때문이다. 이러한 배경에서 나온 암호화 방식이 바로 비대칭 키 방식이다.

### 비대칭 키 (공개 키)

비대칭 키는 공개 키와 개인 키로 암호화 및 복호화를 수행한다. 즉, 공개 키로 데이터를 암호화하면 반드시 개인 키로만 복호화 가능하고, 개인 키로 데이터를 암호화하면 공개 키로만 복호화 할 수 있다. 참고로 개인 키는 비밀 키 혹은 비공개 키라고도 부른다. 비대칭 키는 보안성이 좋지만, 구현이 하기 어렵고 암호화 및 복호화 속도가 느리다는 단점이 있다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F23eda87f-09b3-4fa8-b413-b2e26929d380%2FUntitled.png?table=block&id=aac65669-e23f-4531-bc07-aefa2a50b97d&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

**암호화 - 공개 키, 복호화 - 개인 키**

진짜 데이터를 암호화하여 보호하기 위한 목적이다.

**암호화 - 개인 키, 복호화 - 공개 키**

인증을 위한 목적이다. 즉, 서버에서 개인 키로 데이터를 암호화해서 보냈고 클라이언트에서 공개 키로 복호화가 된다면 해당 서버는 클라이언트 입장에서 신뢰할 수 있다고 판단할 수 있다.

### 대칭 키와 비대칭 키를 혼합하여 사용

SSL은 대칭 키 방식과 비대칭 키 방식을 섞어서 사용한다. SSL Handshake를 소개하기 전에 이 부분도 짚고 넘어 가자.

1. A가 B로 접속 요청을 보낸다.
2. B는 A에게 자신의 공개 키를 전송한다. (B는 미리 공개 키와 개인 키를 만들어 두었다고 가정)
    
    
    ![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F154e3036-e2b1-46df-a5b6-a04d172991a1%2FUntitled.png?table=block&id=e5768e2b-e543-4573-983f-a649e2c03e96&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)
    
3. A는 자신의 대칭 키를 B에서 전달 받은 B의 공개 키로 암호화한다.
    
    
    ![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F433ed4ee-4442-493a-b05b-10438480b2f6%2FUntitled.png?table=block&id=472c5d14-d192-4cf6-a6bc-00f84321ad91&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)
    
4. 이렇게 암호화한 자신의 대칭 키를 B에게 전달한다.
5. B는 자신의 개인 키로 복호화 하여 A의 대칭 키를 얻어 낸다.
    
    
    ![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F3317599a-436f-4655-bd07-9262f7ed514b%2FUntitled.png?table=block&id=4dfb7aca-2edf-460a-a5dc-29daa7461fa2&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)
    
6. 이렇게 얻어낸 대칭 키를 활용해서 A와 B는 안전하게 통신한다.
    
    
    ![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F554e51a2-ffe4-4b2b-bfd0-8b1fbcedf47d%2FUntitled.png?table=block&id=2fb03909-41f0-4ab6-8ae6-a3101a1b2cd7&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)
    

즉, 데이터 암호화와 복호화를 위한 한 쪽의 대칭 키를 다른 쪽의 공개 키로 암호화하여 전송하면, 반대편에서 자신의 개인 키로 복호화하여 그 반대편의 대칭 키를 알아내고, 이 대칭 키를 바탕으로 통신을 하게 된다.

## SSL Handshake의 동작 과정

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F5b346abc-2d80-445e-a483-38aad7f95e35%2FUntitled.png?table=block&id=41600695-95be-452e-b572-330710e50728&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

파란색 칸과 노란색 칸은 네트워크 상에서 전달되는 IP 패킷을 표현한 것이다. 파란색 칸에 해당하는 SYN, SYN ACK, ACK는 TCP 레이어의 3-way handshake로, HTTPS가 TCP 기반의 프로토콜이므로 SSL Handshke에 앞서 연결을 생성하기 위해 실시하는 과정이다. 노란색 칸에 해당하는 패킷들이 SSL Handshake라고 보면 된다.

해당 과정을 설명하기에 앞서 CA와 인증서의 개념을 살펴 보자.

### CA

인증서의 역할은 클라이언트가 접속한 서버가 의도한 서버가 맞는지 보장하는 것이다. 이 역할을 하는 민간 기업들이 있는데 이런 기업들을 CA(Certificate authority) 혹은 Root Certificate라고 부른다. CA는 신뢰성이 엄격하게 공인된 기업들만 참여할 수 있다.

개발자 입장에서 HTTPS를 적용하려면 신뢰할 수 있는 CA 기업의 인증서를 구입해야 한다. 이 인증서를 구입하게 되면 CA 기업의 개인 키를 이용하여 암호화 한 인증서를 준다.

**SSL 인증서**

SSL 인증서에는 서비스의 정보 (인증서를 발급한 CA, 서비스의 도메인 등)와 서버 측 공개 키가 들어 있다.

그러면 서버에서는 본인의 개인 키를 생성하였고, 클라이언트가 사용할 공개 키는 인증서 안에 담겨 있는데, 클라이언트는 CA의 개인 키로 암호화 된 인증서를 어떻게 복호화 할까?

바로, 우리가 사용하는 일반적인 브라우저에 신뢰할 수 있는 CA 기관의 리스트와 해당 기관의 공개키를 이미 가지고 있다. 그래서 클라이언트는 내장된 CA의 공개 키를 활용하여 인증서를 복호화함으로써 인증서를 검증한 뒤, 서버의 공개 키를 가져올 수 있다.

### ClientHello

클라이언트가 서버에 연결을 시도하며 전송하는 패킷이다. 자신이 사용 가능한 Cipher Suite 목록, Session ID, SSL 프로토콜 버전, Random Byte 등을 전달한다. Cipher Suite는 SSL 프로토콜 버전, 인증서 검정, 데이터 암호화 프로토콜, Hash 방식 등의 정보를 담고 있는 존재로, 선택된 Cipher Suite의 알고리즘에 따라 데이터를 암호화하게 된다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F32e95a30-ba76-4010-a285-cc2585e22a1f%2FUntitled.png?table=block&id=fe6c5244-e0fd-4104-9b3b-17b961e25cc2&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

위 사진처럼 클라이언트가 사용 가능한 Cipher Suite를 서버에 제공하는 것을 알 수 있다. 참고로 Cipher Suite의 구성은 다음과 같다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fe38adbc9-7142-4f0d-94a5-5fbf01d44337%2FUntitled.png?table=block&id=090005e9-b478-43ce-9e0c-6c4098284cb3&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### ServerHello

서버는 클라이언트가 보내 온 ClientHello 패킷을 받아 Cipher Suite 중 하나를 선택한 다음 클라이언트에게 이를 알린다. 또한, 자신의 SSL 프로토콜 버전 등도 같이 보낸다. 아래 사진을 보면 ClientHello에서 17개였던 Cipher Suite와 달리 Sever가 선택한 한 줄만 존재하는 것을 알 수 있다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fc5b99fd0-bacb-4b65-a7c4-ab4fa5b84921%2FUntitled.png?table=block&id=5b1da355-68a8-4857-8e0f-8c0542d767fd&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### Certificate

Server가 자신의 SSL 인증서를 클라이언트에게 전달한다. 인증서 내부에는 Server가 발행한 공개 키 (개인 키는 따로 서버가 소유)가 들어있다. 클라이언트는 서버가 보낸 CA (Certificate Authority, 인증 기관)의 개인 키로 암호화된 이 SSL 인증서를 이미 모두에게 공개된 CA의 공개 키를 사용하여 복호화한다. 복호화에 성공하면 이 인증서는 CA가 서명한 것이 맞으니 진짜임을 검증할 수 있다.

또한 클라이언트는 데이터 암호화에 사용할 대칭 키를 생성한 후 SSL 인증서 내부에 들어 있던 서버가 발행한 공개 키를 이용해 암호화하여 서버에게 전송한다. 아래를 보면 SSL 인증서가 무슨 알고리즘으로 암호화되었고, 무슨 Hash 알고리즘으로 서명되었는지 확인할 수 있다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fc4eeb654-6287-435d-8fc7-25bb3ea67e7b%2FUntitled.png?table=block&id=8d8eaebd-0f5a-48ef-bdd8-b855279755bd&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### Server Key Exchange / ServerHello Done

서버의 공개 키가 SSL 인증서 내부에 없는 경우, 서버가 직접 전달한다는 뜻이다. 공개 키가 SSL 인증서 내부에 있을 경우 Server Key Exchange는 생략된다.

인증서 내부에 서버의 공개 키가 있다면, 클라이언트가 CA의 공개 키를 통해 인증서를 복호화 한 후 서버의 공개 키를 확보할 수 있다. 그리고 서버가 행동을 마쳤음을 전달한다.

### Client Key Exchange

대칭 키를 클라이언트가 생성하여 SSL 인증서 내부에서 추출한 서버의 공개 키를 이용해 암호화한 후 서버에게 전달한다. 여기서 전달된 대칭 키가 SSL Handshake의 목적이자 가장 중요한 수단인 데이터를 실제로 암호화할 대칭 키다. 이제 키를 통해 클라이언트와 서버가 실제로 교환하고자 하는 데이터를 암호화한다.

### ChangeCipherSpec / Finished

ChangeCipherSpec 패킷은 클라이언트와 서버 모두가 서로에게 보내는 패킷으로, 교환할 정보를 모두 교환한 뒤 통신할 준비가 다 되었음을 알리는 패킷이다. 그리고 Finished 패킷을 보내어 SSL Handshake를 종료하게 된다.

### 한 줄 정리

서버는 CA에 사이트 정보와 공개 키를 전달하여 인증서를 받음 → 클라이언트는 브라우저에 CA 공개 키가 내장되어 있다고 가정 → ClientHello(암호화 알고리즘 나열 및 전달) → ServerHello(암호화 알고리즘 선택) → Server Certificate(인증서 전달) → Client Key Exchange(데이터를 암호화 할 대칭 키 전달) → Client / ServerHello done (정보 전달 완료) → Finished(SSL Handshake 종료)

### 그림으로 보는 SSL Handshake

1. 사이트는 CA에 사이트 정보와 사이트 공개 키를 보낸다.
    
    
    ![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F1317a5f5-613e-4fce-a97f-90ca2a28b4ca%2FUntitled.png?table=block&id=a356f3f4-4ef1-4300-9e0d-dbaa8c570333&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)
    

2. CA는 자신의 개인 키로 사이트 정보와 사이트 공개 키에 대해 암호화하여 인증서를 생성한 뒤, 인증서를 사이트에게 전달한다.
    
    
    ![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fb93ef659-1955-45e7-a578-1a6f0d0172ea%2FUntitled.png?table=block&id=8746883e-917d-43e8-95ad-8569039ff746&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)
    

3. 사용자는 CA의 공개 키가 브라우저에 내장되어 있다고 가정한다. (이로 인해 Server Key Exchange는 생략됨)
4. 사용자는 사이트에 접속을 요청한다. (ClientHello)
5. 사이트는 사용자의 Cipher Suite 중 하나를 고르고, 자신의 SSL 프로토콜 버전을 사용자에게 알린다. (SeverHello)
6. 사이트는 자신의 사이트 인증서를 사용자에게 전송한다. (Certificate)
    
    
    ![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F8204f355-f1a7-4030-a665-c6cfcff5c437%2FUntitled.png?table=block&id=a05dc0b7-1366-45b3-8abe-618c2f4e0eab&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)
    
7. 사용자는 브라우저에 내장된 CA의 공개 키를 이용하여 사이트 인증서를 복호화하면서 인증서가 유효한지 검증하고, 사이트의 공개 키를 가져온다. (Certificate & ServerHello Done)
    
    
    ![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fa2a55557-c0f8-4ff3-a627-8daa5e3e6470%2FUntitled.png?table=block&id=9bae1ccb-9c1f-48fb-8b41-72695cb590ac&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)
    

8. 사용자는 자신이 전달할 데이터를 1차적으로 암호화한 대칭 키를 만들고, 그 대칭 키를 사이트 공개 키로 암호화한다. (Client Key Exchange)
    
    
    ![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F9b50cbcb-a15d-4b30-8cc5-e08459644719%2FUntitled.png?table=block&id=fb962e7d-bb2e-4c83-be8c-e05bee5d8eb4&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)
    

9. 암호화한 대칭 키를 사이트에게 전달한다. (사용자 입장: ChangeCipherSpec, Finished)
10.  사이트는 자신의 개인 키를 사용하여 사용자 대칭 키를 복호화하여 사용자 대칭 키를 얻어 낸다. (사이트 입장: ChangeCipherSpec, Finished)  
    ![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F05b88f3f-f35e-43de-af62-e56fb2fb6c95%2FUntitled.png?table=block&id=2fad553d-d727-4758-9e77-1269ed5f1cb5&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)
11. 이렇게 얻은 대칭 키를 활용하여 서로가 서로의 데이터를 안전하게 복호화 하면서 통신할 수 있다.  (SSL Handshake 종료)
    
    
    ![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fe3c8f76d-6bfa-41d8-90ef-9e79c058e581%2FUntitled.png?table=block&id=faee7c59-1e73-41aa-94f4-1bdcccc2ad2b&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)
    

## 출처

[https://aws-hyoh.tistory.com/entry/HTTPS-통신과정-쉽게-이해하기-3SSL-Handshake](https://aws-hyoh.tistory.com/entry/HTTPS-%ED%86%B5%EC%8B%A0%EA%B3%BC%EC%A0%95-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-3SSL-Handshake)

[https://coding-start.tistory.com/208](https://coding-start.tistory.com/208)

테코톡 - 다니의 HTTPS ([https://www.youtube.com/watch?v=wPdH7lJ8jf0](https://www.youtube.com/watch?v=wPdH7lJ8jf0))

## 예상 면접 질문 및 답변

### SSL Handshake란?

Handshake는 악수를 의미하는데, 통신을 하는 브라우저와 웹 서버가 서로 암호화 통신을 시작할 수 있도록 신분을 확인하고 필요한 정보를 클라이언트와 서버가 주거니 받거니 하는 과정이 악수와 비슷하여 붙여진 이름이다.

### 대칭 키란?

하나의 키로 데이터를 암호화하고 복호화하는 것을 의미한다. 암호화 및 복호화에 드는 비용이 적지만, 대칭 키가 유출되면 암호의 내용이 쉽게 노출된다는 단점이 있다.

### 비대칭 키란?

비대칭 키는 공개 키와 개인 키로 암호화 및 복호화를 수행한다. 즉, 공개 키로 데이터를 암호화하면 반드시 개인 키로만 복호화 가능하고, 개인 키로 데이터를 암호화하면 공개 키로만 복호화 할 수 있다. 보안성이 뛰어나다는 장점이 있지만, 구현하기가 어렵고 암호화 및 복호화 속도가 느리다는 단점이 있다.

### SSL Handshake 과정을 손으로 그려 보세요.

실제 면접에서 나왔었는데 매우 당황했었음. 위에서 설명한 "**대칭 키와 비대칭 키를 혼합하여 사용**" 챕터를 기본적으로 확실하게 이해하여 설명하는 것을 기본으로 두고, 인증 기관까지 질문이 나오면 "**그림으로 보는 SSL Handshake**" 챕터 부분을 설명하면서 그림을 그리면 된다.

### SSL Handshake의 과정을 설명해 보세요.

서버는 CA에 사이트 정보와 공개 키를 전달하여 인증서를 받음 → 클라이언트는 브라우저에 CA 공개 키가 내장되어 있다고 가정 → ClientHello(암호화 알고리즘 나열 및 전달) → Serverhello(암호화 알고리즘 선택) → Server Certificate(인증서 전달) → Client Key Exchange(데이터를 암호화 할 대칭 키 전달) → Client / ServerHello done (정보 전달 완료) → Finished(SSL Handshake 종료)

위와 같은 흐름으로 설명할 수 있어야 하고, 그림으로도 그릴 줄 알아야 한다.
