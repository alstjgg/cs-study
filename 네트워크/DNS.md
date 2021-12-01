## 1. 등장 배경

네트워크 안에서 호스트들을 식별하기 위한 목적으로 IP 주소를 사용하는데, 사람의 경우 숫자보다 문자를 사용하는 것이 더 편해 도메인 이름을 사용하여 호스트를 식별한다.

도메인 이름을 사용하는 경우에도 최종적으로 IP 주소를 알고 있어야 상대방과 연결이 가능하기 때문에 네트워크에서 도메인이나 호스트 이름을 숫자로 된 IP 주소로 해석해주는 TCP/IP Network Service인 DNS가 등장했다.

## 2. DNS란?

### 정의

TCP/IP 네트워크 상에서 사람이 기억하기 쉽게 문자로 만들어진 도메인을 숫자로된 IP 주소로 바꾸는 서버를 DNS라고 한다.

### 포트번호

UDP와 TCP 포트 53번을 사용한다.

DNS 서버에 질의를 보낼 때 UDP를 사용하고, Zone Transfer(영역 전송)과 512Byte를 초과하는 패킷을 전송해야 할 경우 TCP를 사용한다.

> **Zone Transfer(영역 전송)이란?**  
> DNS 서버는 존 내의 호스트들에 대한 정보를 담고 있는 데이터베이스를 가지고 있으며,  복수의 보조 서버들은 기본 서버 데이터의 복사본을 저장하고 있다. 기본 서버의 정보를 이용해 보조 서버의 데이터를 최신 상태로 업데이트하는 작업을 영역 전송이라고 한다.

### DNS가 UDP를 사용하는 이유

**1) 속도가 빠르다.**

TCP의 경우 데이터전송 시작 전에 3-way-handshaking 과정이 있는 반면 UDP는 이런 과정이 없어 연결 설정에 드는 비용이 없다. DNS는 신뢰성보다 속도가 더 중요한 서비스이기 때문에 TCP보다 UDP가 더 적합하다. 또한 DNS의 경우 전송하는 데이터 패킷 사이즈가 매우 작다. UDP의 경우 512bytes를 넘어가지 않는 패킷만 전송이 가능하고 오버헤드가 없어 매우 빠른 속도를 가진다.

이때 단순히 패킷의 사이즈가 작다고 TCP 대신 UDP를 사용하는 것이 아니라, 전달하는 패킷의 크기가 작기 때문에 신뢰성이 보장되지 않아도 되기 때문입니다. (못 받으면 다시 전달하면 된다.)

**2) 연결 상태를 유지할 필요가 없다.**

TCP는 호스트들 간의 연결 상태를 유지하며 패킷 안에 여러 정보들이 담겨 있습니다. 반면 UDP는 어떤 정보도 담지 않고 유지할 필요가 없고 TCP보다 UDP에서 동작할 때 더 많은 클라이언트를 수용할 수 있다.

따라서 DNS 서버는 많은 클라이언트를 수용해야 하기 때문에 연결 상태를 유지하지 않고 정보 기록을 최소화할 수 있는UDP를 사용한다.

## 3. DNS의 과거와 현재

### 과거

과거에는 컴퓨터마다 hosts.txt 파일을 가지고 있었다.

![image](https://user-images.githubusercontent.com/55661631/144170786-df041595-26d7-4e8a-b746-002b4d969369.png)

hosts 파일에는 모든 컴퓨터의 도메인 이름과 IP 주소 정보가 저장되어있고, 클라이언트는 FTP를 이용해 hosts 파일을 다운로드 하였다.

그러나 90년대 초반 웹 서비스 사용자가 폭발적으로 증가하면서 인터넷에 연결된 호스트 수가 늘어 났다. 결국 호스트의 업데이트가 늦어지고 네트워크 트래픽이 증가하여 많은 문제들이 발생했다.

### 현재

도메인이 많기 때문에 전 세계 모든 조직의 도메인 정보를 갖고 있는 DNS 서버는 존재하지 않는다. 각 조직은 자신들의 도메인 정보를 관리하는 DNS 서버를 운영하고, 이러한 수 많은 DNS 서버들이 상호 연동되어 있는 Domain Name Space를 구성하게 되었다.

## 4. DNS 서버의 종류

![image](https://user-images.githubusercontent.com/55661631/144170815-bfd84ee9-e8e7-44e1-89cb-89be489b377f.png)

존재하는 도메인 수가 매우 많기 때문에, 위 그림과 같이 DNS 서버 종류를 계층화해서 단계적을 처리한다. DNS 서버는 크게 권한 없는 네임 서버(non-Authoritative Name Server)와 권한 있는 네임 서버(Authoritative Name Server)로 나누어 진다.

### 권한 없는 네임 서버 (non-Authoritative Name Server)

#### 1) Recursive DNS Server

인터넷 사용자가 가장 먼저 접근하는 DNS 서버이다. 매번 3개의 DNS 서버를 거친다면 효율이 떨어지므로, 한 번 DNS 서버에 요청해 얻은 데이터를 일정 기간 동안 캐시라는 형태로 저장해두는 서버이다.

직접 도메인과 IP 주소의 관계를 기록/저장/변경하지 않고 캐시만을 보관하기 때문에, Authoritative와 비교되는 의미로 non-Authoritative, Recursive라는 이름을 붙었다.

대표적으로 KT/LG/SK와 같은 ISP(통신사) DNS 서버가 있고, 브라우저 우회 용도로 많이 쓰는 구글 DNS, 클라우드 플레어와 같은 Public DNS 서버가 있다.

### 권한 있는 네임 서버 (Authoritative Name Server)

#### 1) Root DNS Server

ICANN이 직접 관리하는 서버로, TLD(최상위 도메인) DNS 서버 IP 주소들을 저장해 두고 안내하는 역할을 한다.

#### 2) TLD(최상위 도메인) DNS Server

도메인 등록 기관(Registry)이 관리하는 서버로, Authoritative DNS 서버 주소를 저장해두고 안내하는 역할을 한다.

어떤 도메인 묶음이 어떤 Authoritative DNS Server에 속하는지 아는 이유는 도메인 판매 업체(Registrar)의 DNS 설정이 변경되면 도메인 등록 기관(Registry)으로 전달이 되기 때문이다.

#### 3) Authoritative DNS Server

실제 개인 도메인과 IP 주소의 관계가 기록/저장/변경되는 서버이다. 그래서 권한의 의미인 Authoritative가 붙었다고 한다.

일반적으로 도메인/호스팅 업체의 ‘DNS 서버’를 말하지만, 개인 DNS 서버 구축을 한 경우에도 여기에 해당한다.

## 5. DNS 리소스 레코드

DNS 서버는 데이터베이스 서버의 한 유형이며, 클라이언트로부터 질의를 받았을 때 그에 맞는 데이터를 응답해야 한다. 데이터베이스의 한 항목(Row)을 DNS 서버에서는 리소스 레코드라고 부른다.

![image](https://user-images.githubusercontent.com/55661631/144170910-8c80b049-83ca-470b-ba97-be4152d8cf71.png)

타입(Type)에는 다음과 같은 여러 종류가 있습니다.

-   SOA : 해당 Domain 관리 권한 및 Zone Transfer(영역 전송)과 관련된 정보가 들어있다.
-   NS : NameServer의 정보를 갖고 있다.
-   A(AAAA) : 특정 host의 FQDN과 연결된 IP주소 정보를 갖는다.
-   CNAME : 특정 A레코드에 대한 별칭을 지정한다.
-   MX : Mail eXchange의 약자로 Mail 서비스에 관련된 정보를 갖고 있다. (해당 Domain의 Mail서버 정보)
-   PTR : 역방향 조회에 사용되는 레코드, 특정 IP주소에 대한 FQDN 정보를 가지고 있다.
-   ANY : 도메인에 대한 모든 레코드 질의 시에 주로 이용된다. (DNS 증폭 DRDOS 공격에 악용)

## 6. DNS 동작 과정

DNS의 동작 과정을 살펴보기 앞서 도메인 구조와 클라이언트가 DNS 서버에게 어떤 형태로 질의를 보내는지 살펴보자.

### 도메인 구조

![image](https://user-images.githubusercontent.com/55661631/144170941-698c696c-6510-47ca-89a3-f4b2edf3d599.png)

통상적으로 도메인의 가장 끝에 있는 점(.)은 생략되어 있다. 이것을 Root 도메인이라고 부른다. Root 도메인의 직속 하위 도메인을 Top-level 도메인이라고 부르며 com, net, co.kr 등이 있다. Top-level 도메인 직속 하위 도메인은 Second-level 도메인이라고 부르고 그 직속 하위 도메인을 Sub 도메인이라고 한다.

DNS에서 통용되는 규칙 몇 가지가 있다.

-   직속 하위 레벨의 DNS 서버의 리스트를 알고 있어야 한다.
-   모든 컴퓨터는 적어도 Root 도메인의 DNS 서버를 알고 있어야 한다.

### 질의 메시지

클라이언트가 DNS 서버에게 질의를 보낼 때 도메인 주소만 보내는 것이 아니라 다음과 같은 정보도 포함해서 보낸다.

![image](https://user-images.githubusercontent.com/55661631/144170952-8052d5d3-b35d-4313-adb6-35ba37994ec0.png)

-   Query Name String : 요청하고자 하는 도메인 이름
-   Type : 이름의 데이터 타입. 타입에 따라 응답 정보의 내용이 달라진다.
-   Class : 이름의 클래스. DNS의 구조가 처음 고안되었을 때는 인터넷 이외의 네트워크도 고려되어 있었기 때문에 클래스를 두었다. 지금은 인터넷 이외에는 사용하지 않으므로 기본으로 IN이라는 값을 보낸다.

### 동작 과정

![image](https://user-images.githubusercontent.com/55661631/144170954-b1a6b8fb-01af-4c50-9ae8-231fd5d85b32.png)

1. 브라우저에서 Nesite.com을 검색하고, 사용하고 있는 통신사인 KT DNS 서버에게 도메인 주소에 해당하는 IP 주소를 요청한다. (브라우저 기본 DNS 설정은 통신사 DNS 서버이다.)

2. ISP 서버에선 캐시 데이터가 없다는 걸 확인하고 Root DNS 서버에게 어디로 가야 하는지 요청한다. (만약 캐시가 있다면 8로 건너뛰게 된다.)

3. Root DNS 서버는 TLD DNS 서버 주소만 관리하기 때문에, \*\*\*. com도메인을 보고는 com 최상위 도메인을 관리하는 TLD DNS 서버 주소를 안내한다.

4. ISP 서버는 com 서버에게 어디로 가야 하는지 다시 요청한다.

5. com 서버는 가비아 DNS 서버에서 해당 도메인이 관리되고 있는 걸 확인하고 가비아 DNS 서버 주소를 안내한다.

6. ISP 서버는 가비아 서버에게 다시 요청한다.

7. 가비아 서버는 요청받은 도메인 주소의 IP 주소를 확인하고 알려준다. 동시에 ISP 서버는 해당 정보를 캐시로 기록한다.

8. ISP 서버는 브라우저에게 알아낸 IP 주소를 안내한다.

## 참고

-   [gentlysallim 블로그](http://https://gentlysallim.com/dns%EB%9E%80-%EB%AD%90%EA%B3%A0-%EB%84%A4%EC%9E%84%EC%84%9C%EB%B2%84%EB%9E%80-%EB%AD%94%EC%A7%80-%EA%B0%9C%EB%85%90%EC%A0%95%EB%A6%AC/)
-   [https://borisoop.tistory.com/399](https://borisoop.tistory.com/399)
-   [https://peemangit.tistory.com/52](https://peemangit.tistory.com/52)[https://esoongan.tistory.com/m/148](https://esoongan.tistory.com/m/148)
-   [https://devowen.com/406](https://devowen.com/406)
-   [https://www.netmanias.com/ko/post/blog/5353/dns/dns-basic-operation](https://www.netmanias.com/ko/post/blog/5353/dns/dns-basic-operation)
-   [https://m.blog.naver.com/wnrjsxo/221253031733](https://m.blog.naver.com/wnrjsxo/221253031733)

## 예상 면접 질문 및 답변

### Q. DNS란?
A. TCP/IP 네트워크 상에서 사람이 기억하기 쉽게 문자로 만들어진 도메인을 숫자로된 IP 주소로 바꾸는 서버를 DNS라고 한다.

### Q. DNS 서버에게 IP 주소를 요청할 때 UDP를 사용하는 이유는?
A. DNS는 신뢰성보다 속도가 더 중요하기 때문에 속도가 빠르고, 많은 클라이언트를 수용해야 하기 때문에 연결 상태를 유지하지 않고 정보 기록을 최소화할 수 있는 UDP를 사용한다.

### Q. DNS 서버의 종류에 대해 설명
A. Root DNS Server, TLD DNS Server, Authoritative DNS Server, Recursive DNS Server로 나누어집니다. (각 서버에 대한 설명은 위 글을 참고)

### Q. DNS 서버를 여러 종류로 나눈 이유?
A. 존재하는 도메인 수가 많기 때문에 하나의 DNS 서버만 사용한다면 성능, 유지관리 등 많은 문제가 생긴다. 따라서 DNS 서버 종류를 계층화해 단계적으로 처리하여 트래픽을 분산시키고 유지관리를 안정적으로 하기 위해 나누었다.

### Q. DNS의 동작과정을 설명
A. 위 글을 참고



