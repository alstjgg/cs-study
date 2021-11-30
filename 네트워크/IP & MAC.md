## IP란?

### 정의

IP는 인터넷 프로토콜의 약자로, 송신 호스트와 수신 호스트가 패킷 교환 네트워크에서 정보를 주고받는 데 사용하는 정보 위주의 규약이며, OSI 네트워크 계층에서 호스트의 주소 지정과 패킷 분할 및 조립 기능을 담당한다.

### 특징

비신뢰성과 비연결성이 특징이다. 비신뢰성은 흐름에 관여하지 않기 때문에 보낸 정보가 제대로 갔는지 보장하지 않는다는 뜻이다. 예를 들어 전송 과정에서 패킷이 손상될 수도 있고, 같은 호스트에서 전송한 패킷의 순서가 뒤죽박죽되거나, 같은 패킷이 두 번 전송될 수도 있고, 아예 패킷이 사라질 수도 있다. 그래서 패킷 전송과 정확한 순서를 보장하려면 TCP 프로토콜과 같은 IP의 상위 프로토콜을 이용해야 한다.

## IP 주소

IP 주소는 네트워크 환경에서 컴퓨터(노드) 간 통신하기 위해 각 컴퓨터에 부여된 네트워크 상 주소이다. 전 세계의 IP 주소는 미국의 'Inter NIC'에서 총괄적으로 관리하며 분배하고 있다. 우리나라의 경우 '한국 인터넷 정보 센터(KRNIC)'에서 'Inter NIC'로부터 할당 받은 IP를 다시 각 인터넷 사업체를 통해 해당 서비스의 사용자에게 IP 주소를 할당하고 있다.

### IP 주소 형식

**IPv4 주소**

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F36195fc0-e486-44e6-aa3c-185ca64ce685%2FUntitled.png?table=block&id=a3fa3067-ae7c-4450-a633-2f3e429aba56&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

IP 주소는 보통 4개의 10진수로 이루어진 IPv4 체계를 택하고 있는데, 각 10진수는 8자의 2진수로 변환되며, 총 32개의 0과 1로 이루어져 있다. 즉 0.0.0.0부터 255.255.255.255까지 약 42억 개의 주소를 표현할 수 있다.

**IPv6 주소**

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F3e920776-2310-4779-b9a5-4cc4e4e013f8%2FUntitled.png?table=block&id=86e67aa5-c45f-474b-9921-d724f0bac289&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

IP 주소라는 개념이 처음 생겼을 당시에는 지금처럼 네트워킹이 가능한 장비의 종류가 다양하지 않았지만, 기술이 발전하고 한 사람이 가지는 네트워킹 단말기가 증가하면서 IPv4 주소의 수가 부족해졌다. 이를 해결하기 위해 주소 길이를 128 비트로 늘려 사용 가능한 주소의 개수를 2의 128제곱 개 늘린 IPv6 주소가 등장했다.

IPv6 주소는 보통 두 자리 16진수 여덟 개를 쓰고 각각 `:` 기호로 구분한다.

**IPv4 vs IPv6**

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fff7c766d-b1e8-4bb6-9420-a7e79b2aa05e%2FUntitled.png?table=block&id=298b66f4-cbda-4a21-83c7-8721846e0967&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

## IP 주소의 클래스란?

IP 주소에는 클래스라는 개념이 있고 클래스는 하나의 IP 주소에서 네트워크 주소(ID)와 호스트 주소(ID)를 나누는 방법이자 약속이다. 네트워크 주소는 모든 호스트들을 관리하기가 어렵기 때문에 네트워크에 범위를 그룹(호스트의 집합)으로 지정해 어떤 네트워크인지 나타내고, 호스트 주소는 네트워크 그룹 내에서 어느 컴퓨터인지를 나타낸다. IP 주소를 여러 클래스로 나누는 이유는 네트워크 크기에 따라 구분하기 위해서다. 네트워크 범위가 커질수록 호스트 주소 범위는 작아지는 반비례 관계이기 때문이다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fd3d85f6a-a48a-4cc7-920f-13f0af4165ef%2FUntitled.png?table=block&id=8af91102-c513-4d0a-b5dc-13de9aec11d0&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

주로 A, B, C  클래스만 사용한다. D, E 클래스는 멀티캐스트용, 연구용으로 사용하기 때문이다.

### A 클래스

A 클래스는 하나의 네트워크가 가질 수 있는 호스트 수가 제일 많다. 맨 앞자리 수가 항상 0인 경우가 A 클래스이며, A 클래스에서 가질 수 있는 IP의 범위는 다음과 같다.

```java
# 2진수
0000 0000. 0000 0000. 0000 0000. 0000 0000 ~ 0111 1111. 1111 1111. 1111 1111. 1111 1111

# 10진수
1.0.0.0 ~ 126.255.255.255
```

A 클래스에서 네트워크 주소는 1부터 126으로 시작한다. 0과 127은 사용할 수 없도록 약속으로 정의해 두었기 때문이다. 이후 3가지 옥텟(`.` 으로 구분된 영역)은 호스트 주소에 해당하며, `(2^24) - 2` 개만큼 주소를 가질 수 있다. 호스트 주소가 `0.0.0` 이면 전체 네트워크에서 작은 네트워크를 식별할 때 쓰이고, 호스트 주소가 `255.255.255` 이면 브로드캐스트 주소로 사용하기 때문이다. 브로드캐스트 주소는 해당 네트워크에 있는 컴퓨터나 장비 모두에게 한 번에 데이터를 전송할 때 사용되는 주소이다.

### B클래스

B 클래스의 IP 주소를 32자리 2진수로 표현했을 때, 맨 앞자리 수는 항상 `10` 이여야 한다. 즉 `10xx xxxx. xxxx xxxx. xxxx xxxx. xxxx xxxx` 이며, 10진수로 표현하면 `128.0.0.0 ~ 191.255.255.255` 이다.

네트워크 주소 범위는 `10xx xxxx. xxxx xxxx` 에서 2^14개, 호스트 주소 범위는 `xxxx xxxx. xxxx xxxx` 에서 (2^16) - 2개이다.

### C클래스

C 클래스의 IP 주소를 32자리 2진수로 표현했을 때, 맨 앞자리 수는 항상 `110` 으로 시작한다. 즉 `110x xxxx. xxxx xxxx. xxxx xxxx. xxxx xxxx` 이며, 10진수로 표현하면 `192.0.0.0 ~ 223.255.255.255` 이다.

네트워크 주소 범위는 `110x xxxx. xxxx xxxx. xxxx xxxx`에서 2^21개, 호스트 주소 범위는 `xxxx xxxx` 에서 (2^8) - 2개이다.

### 표로 보는 클래스

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F1186a8ab-45f0-4fd1-96eb-857e681f3400%2FUntitled.png?table=block&id=c4107bf5-9ec3-4440-85ae-6dd40e4fbfb9&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

## IP 주소 분류

IP 주소는 사용 범위에 따라 공인 IP와 사설(가상) IP, 할당 방식에 따라 고정 IP와 유동 IP로 나눌 수 있다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fe06f68a1-1841-469b-800a-742a6e7005c3%2FUntitled.png?table=block&id=5fdc5085-1308-4693-84f2-c44c17296153&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### 공인 IP 주소 vs 사설(가상) IP 주소

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F22fa2bad-5b49-42d2-ac16-b7f66e55d5da%2FUntitled.png?table=block&id=9f296ea9-ba50-4944-8fbd-586b3ceb9241&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

공인 IP 주소는 공인 기관에서 할당한 IP 주소이며, 통신에서 쓰이는 실제 주소이자 공개된 주소이다. 공인 IP 주소를 알면 누구라도 그 주소를 토대로 연결된 기기에 접근이 가능하다.

사설 IP 주소는 우리가 흔히 쓰는 공유기를 통해 인터넷에 접속하게 되면 사설 IP 주소가 부여된다. 실제로 한 공유기를 통해 여러 기기를 사용하듯, 공유기에 공인 IP를 부여하고 그에 연결된 각 기기에는 사설 IP를 부여한다.

참고로 공유기는 엄밀히 말하면 공인 IP와 사설  IP를 모두 가지고 있으며, 외부에서는 공인 IP를 사용하고 내부에서는 사설 IP를 사용한다. NAT 기능을 사용해서 사설  IP를 공인 IP로 바꿀 수도 있다.

### 고정 IP 주소 vs 유동 IP 주소

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fdf8e475c-db8f-4013-9afa-6eefdcded392%2FUntitled.png?table=block&id=30dc62d0-8073-4a48-aea5-7e8a3d31eb04&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

보통의 가정에서는 사설 IP를 사용하는데, 이는 컴퓨터가 네트워크에 접속할 때마다 서버에 IP 주소 할당을 요청하고, 서버는 IP 주소 등의 네트워크 정보를 다시 전달해 최종적으로 컴퓨터가 IP 주소를 등록하는 과정을 거친다. 즉, 네트워크에 접속할 때마다 IP 주소가 변경될 수 있다.

일반적인 경우 IP가 변동되어도 인터넷을 이용하는 데 문제가 없지만, NAS나 FTP를 이용한 파일 공유나 서버 구축을 할 때 IP가 변하면 매번 수정해야하므로 이런 경우는 IP를 고정하는 고정 IP를 사용하기도 한다.

## MAC 이란?

Media Access Control의 약자로, 매체 접근 제어 방식을 다루는 프로토콜이다. 단말 간의 충돌/경합 등 연결 방식을 제어하는 역할을 수행한다. MAC 헤더에는 목적지 주소, 발송지 주소, 길이 정보 등이 포함된다.

## MAC 주소란?

MAC 주소는 데이터 링크 계층에서 통신을 위한 네트워크 인터페이스(보통 NIC)에 할당된 고유 식별자이다. 이더넷 하드웨어 주소, 물리적 주소, 하드웨어 주소라고도 불리며, 다른 MAC 주소와 겹치지 않는 특징이 있다.

표준 MAC 주소는 총 48비트(6 bytes)로 구성되어 있으며 `:` 으로 구분한다. ex) `11:32:FF:2C:4B:AB` 

이 중 MAC 주소의 앞 3 bytes(24 bit)는 제조사를 구분할 수 있는 부분으로 제조업체 식별 코드인 OUI라고 부르고, 나머지 뒷 부분은 임의로 고유 번호를 부여한 것이며 호스트 식별자라고 부른다.

### ARP

ARP(Address Resolution Protocol)는 IP 주소를 MAC 주소와 매칭하기 위한 프로토콜이다.

## IP 주소 vs MAC 주소

### MAC 주소가 필요한 이유

통신에서 MAC 주소를 물리적 주소, IP 주소를 논리적 주소라고 말한다. IP 주소만 있으면 될 것 같은데 왜 MAC 주소까지 필요한 걸까?

우리가 미국에서 서울에 살고 있는 상우의 컴퓨터와 통신을 하고 싶다고 가정하자. 이때 상우가 알려준 IP 주소가 상우의 컴퓨터라는 사실을 보증할 수가 없다. 위에서 말한 유동 IP의 특징으로 인해 IP 자체가 변동될 수 있기 때문이다. 결국 절대 변하지 않는 하드웨어의 고유 주소 번호가 필요한데, 그것이 바로 MAC 주소다.

다시, 상우의 컴퓨터 A에게 메일을 보낸다고 가정해 보자. 일단 우리 옆에 사람에게 전달, 또 옆 사람에게 전달하는 방식으로 이웃 간의 통신을 보장해 주는 데이터 링크 계층이고 이때 MAC 주소를 활용한다. 하지만, 컴퓨터는 자신의 IP와 MAC 주소는 알아도 상대방의 MAC 주소는 모른다. 그럼에도 통신은 잘 이루어진다. 왜 그럴까?

우리가 보통 메일을 보내면 xxx@abc.com과 같이 도메인 주소를 입력하는데, 이 도메인 주소는 전송 될 때 IP로 DNS를 통해 변경되며 네트워크 단에서 이 IP 주소로 MAC 주소를 알아 오는 ARP 프로토콜을 사용한다. 이렇게 송수신에 필요한 IP와 MAC 주소를 모두 얻을 수 있게 된다.

### IP 주소가 필요한 이유

MAC 주소가 필요한 것을 알게 되었으니, MAC 주소만 있으면 되는 것이 아닌가 생각할 수 있다. 하지만 이 세계에 장비가 매우 매우 많은데, 상대방의 MAC 주소를 알고 있어도 통신하기가 힘들다. 만약 상대방이 나와 가까운 곳에 있다면 괜찮지만, 한국에서 미국에 있는 A 컴퓨터에 가기 위해서는 나와 연결된 어떤 컴퓨터에게 데이터를 넘겨야 하는지 MAC 주소로 판단하기 어렵다.

이를 해결하기 위해 IP 주소가 필요하다. IP 주소는 전체적인 맵을 보고 방향성을 알려주는 역할을 한다고 생각하면 된다. IP 주소는 네트워크 주소와 호스트 주소로 나뉘므로 실생활에서 우편물이나 택배를 보낼 때 사용하는 주소 원리와 유사하다.

미국에 있는 제이온이 한국 친구 상우에게 편지를 보낸다고 생각해 보자. 우체국에 단순히 상우에게 편지를 보내 달라고 하면, 우체국은 어디로 편지를 보내야 하는지 알 수가 없을 것이다.  (상우는 이름이 절대 변하지 않는 MAC 주소 역할을 한다고 가정하겠습니다.) 그래서 '서울특별시 강남구 대치동' 이런 식으로 편지를 보낼 위치를 적어야 한다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F583c4fd1-f092-4529-b191-5582c589f65d%2FUntitled.png?table=block&id=1b8b984a-dd3c-4fe4-be9c-b31dcea2f311&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

우체국은 이제 맨 상위 계층 서울특별시를 우편을 싹 다 서울 특별시 집합 장소로 보낸다. 그리고 서울 특별시에 모인 우편물을 가지고 강남구로 보내고, 다시 대치동에게 분류해서 내보낸다. 이러한 주소를 계층형 주소라고 하는데, IP 주소도 계층형 주소이므로 IP 주소 체계의 네트워크는 계층 구조 네트워크라고 부른다. 참고로 우편 배달 시스템을 IP 프로토콜, 계층형 주소를 IP 주소라고 생각하면 된다. 이러한 원리로 수많은 컴퓨터 사이에서 범위를 차차 좁혀나가 상우가 어디 있는지 알 수 있다.

### IP 주소 vs MAC 주소

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F57154546-0b8e-4b59-af94-f5f858f20931%2FUntitled.png?table=block&id=637d7c1f-d1df-4ec4-bfb4-07b12f0cf645&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

**MAC 주소**

- 데이터 링크 계층에서 사용되는 주소이다.
- 거리가 가까운 주변 장치끼리 통신할 수 있다.
- 총 48비트(6 bytes)로 구성되어 있으며 `:` 으로 구분한다. ex) `11:32:FF:2C:4B:AB`
- 변하지 않는 값이다.
- 물리적 주소라고도 부른다.
- 제조할 때 장치에 입력된다.

**IP 주소**

- 네트워크 계층에서 사용되는 주소이다.
- 글로벌 단위에서 장치 간의 통신이 가능하도록 도와준다.
- IPv4, IPv6 방식 중 하나를 채택하여 사용한다.
- 때때로 변할 수 있는 값이다.
- 논리적 주소라고도 부른다.
- 소프트웨어 설정을 통해 장치에 할당된다.

## 출처

- [https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=with_msip&logNo=221029109709](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=with_msip&logNo=221029109709)
- [https://velog.io/@hidaehyunlee/IP-address란](https://velog.io/@hidaehyunlee/IP-address%EB%9E%80)
- [https://limkydev.tistory.com/168](https://limkydev.tistory.com/168)
- [http://korean-daeddo.blogspot.com/2015/12/ip.html](http://korean-daeddo.blogspot.com/2015/12/ip.html)
- [https://www.itworld.co.kr/news/105835](https://www.itworld.co.kr/news/105835)
- [https://ko.wikipedia.org/wiki/인터넷_프로토콜](https://ko.wikipedia.org/wiki/%EC%9D%B8%ED%84%B0%EB%84%B7_%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C)
- [https://m.blog.naver.com/xcripts/70121283191](https://m.blog.naver.com/xcripts/70121283191)
- [https://m.blog.naver.com/kim04099/221917309214](https://m.blog.naver.com/kim04099/221917309214)
- [https://aws-hyoh.tistory.com/entry/ARP-쉽게-이해하기](https://aws-hyoh.tistory.com/entry/ARP-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)
- [https://jhnyang.tistory.com/404](https://jhnyang.tistory.com/404)
- [https://www.techtarget.com/searchnetworking/answer/What-is-the-difference-between-an-IP-address-and-a-physical-address](https://www.techtarget.com/searchnetworking/answer/What-is-the-difference-between-an-IP-address-and-a-physical-address)
- [https://almotjalal.tistory.com/98](https://almotjalal.tistory.com/98)

## 예상 면접 질문 및 답변

### IP란?

IP는 인터넷 프로토콜의 약자로, 송신 호스트와 수신 호스트가 패킷 교환 네트워크에서 정보를 주고받는 데 사용하는 정보 위주의 규약이며, OSI 네트워크 계층에서 호스트의 주소 지정과 패킷 분할 및 조립 기능을 담당한다. 비신뢰성과 비연결성이 특징이다.

### IP 주소란?

IP 주소는 네트워크 환경에서 컴퓨터(노드) 간 통신하기 위해 각 컴퓨터에 부여된 네트워크 상 주소이다. 전 세계의 IP 주소는 미국의 'Inter NIC'에서 총괄적으로 관리하며 분배하고 있다. 우리나라의 경우 '한국 인터넷 정보 센터(KRNIC)'에서 'Inter NIC'로부터 할당 받은 IP를 다시 각 인터넷 사업체를 통해 해당 서비스의 사용자에게 IP 주소를 할당하고 있다.

### IPv6 주소가 생겨난 이유는?

IP 주소라는 개념이 처음 생겼을 당시에는 지금처럼 네트워킹이 가능한 장비의 종류가 다양하지 않았지만, 기술이 발전하고 한 사람이 가지는 네트워킹 단말기가 증가하면서 IPv4 주소의 수가 부족해졌다. 이를 해결하기 위해 주소 길이를 128 비트로 늘려 사용 가능한 주소의 개수를 2의 128제곱 개 늘린 IPv6 주소가 등장했다.

### MAC 주소란?

MAC 주소는 데이터 링크 계층에서 통신을 위한 네트워크 인터페이스(보통 NIC)에 할당된 고유 식별자이다. 이더넷 하드웨어 주소, 물리적 주소, 하드웨어 주소라고도 불리며, 다른 MAC 주소와 겹치지 않는 특징이 있다.

### 통신할 때 IP 주소 외에 MAC 주소가 필요한 이유는?

우리가 미국에서 서울에 살고 있는 상우의 컴퓨터와 통신을 하고 싶다고 가정하자. 이때 상우가 알려준 IP 주소가 상우의 컴퓨터라는 사실을 보증할 수가 없다. 위에서 말한 유동 IP의 특징으로 인해 IP 자체가 변동될 수 있기 때문이다. 결국 절대 변하지 않는 하드웨어의 고유 주소 번호가 필요한데, 그것이 바로 MAC 주소다.

### 통신할 때 MAC 주소 외에 IP 주소가 필요한 이유는?

MAC 주소는 데이터 링크 계층 프로토콜이므로 통신할 상대방이 나와 가까운 곳에 있다면 괜찮지만, 한국에서 미국에 있는 A 컴퓨터에 데이터가 가기 위해서는 나와 연결된 어떤 컴퓨터에게 데이터를 넘겨야 하는지 MAC 주소로 판단하기 어렵다. 이를 해결하기 위해 IP 주소가 필요하다. IP 주소는 전체적인 맵을 보고 방향성을 알려주는 역할을 한다고 생각하면 된다. IP 주소는 네트워크 주소와 호스트 주소로 나뉘므로 실생활에서 우편물이나 택배를 보낼 때 사용하는 계층형 주소 원리와 유사하기 때문이다.

### IP 주소와 MAC 주소의 차이점

**MAC 주소**

- 데이터 링크 계층에서 사용되는 주소이다.
- 거리가 가까운 주변 장치끼리 통신할 수 있다.
- 총 48비트(6 bytes)로 구성되어 있으며 `:` 으로 구분한다. ex) `11:32:FF:2C:4B:AB`
- 변하지 않는 값이다.
- 물리적 주소라고도 부른다.
- 제조할 때 장치에 입력된다.

**IP 주소**

- 네트워크 계층에서 사용되는 주소이다.
- 글로벌 단위에서 장치 간의 통신이 가능하도록 도와준다.
- IPv4, IPv6 방식 중 하나를 채택하여 사용한다.
- 때때로 변할 수 있는 값이다.
- 논리적 주소라고도 부른다.
- 소프트웨어 설정을 통해 장치에 할당된다.
