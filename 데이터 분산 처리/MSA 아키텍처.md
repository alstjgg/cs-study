## 모놀리식 아키텍처 (Monolithic Architecture)

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F35d4c4f7-794d-4aa6-9a46-0371072f6343%2FUntitled.png?table=block&id=9c41b3d1-b058-4e90-b76f-9b41975a0875&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

모놀리식 아키텍처는 마이크로서비스(MSA) 아키텍처에 반대되는 개념으로, 애플리케이션의 모든 구성 요소가 한 프로젝트에 통합되어 있는 형태를 말한다.

### 장점

- 개발 초기에 단순한 아키텍처 구조로 인해 개발에 용이하다.
- 어떤 서비스든지 개발되어 있는 환경이 같아서 복잡하지 않다.
- 배포가 간단하다.
- 확장성이 쉽다.
    - 로드밸런스를 이용하여 로드 부하를 나눠 가지는 방식으로 진행한다.
- 쉽게 고가용성 서버 환경을 만들 수 있다.
- End-to-End 테스트가 용이하다.

### 단점

- 프로젝트의 규모가 커짐에 따라 애플리케이션 구동 시간이 늘어나고 빌드 및 배포 시간이 길어진다.
- 조그마한 수정 사항이 있어도 전체를 다시 빌드하고 배포해야 한다.
- 많은 양의 코드가 몰려 있어서 개발자가 모든 코드를 이해하기 힘들며, 유지 보수가 어렵다.
- 일부분의 오류가 전체에 영향을 미친다. (장애가 전파된다.)
- 기술 스택이 한 번 정해지면 바꾸기 어렵다.
- 전체 애플리케이션 확장은 쉽지만, 부하 분산을 위해 각 컴포넌트를 독립적으로 확장하기 어렵다.

## 마이크로서비스(MSA) 아키텍처

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fbf6711a9-07bd-4192-bd00-b002d2ab104b%2FUntitled.png?table=block&id=1063d9d9-52bc-4ca9-873c-d80d407cb91e&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

MSA 아키텍처는 하나의 큰 애플리케이션을 여러 개의 작은 애플리케이션으로 쪼개어 변경과 조합이 가능하도록 만든 형태를 말한다. 작은 레고 블록(microservice) 하나 하나를 붙여 어떠한 큰 결과물을 만드는 레고 놀이를 생각하면 이해하기 쉽다.

### Micro Service

마틴 파울러는 MSA에 대해 아래와 같이 정의하였다.

```
"the microservice architectural style is an approach to developing a single application 
as a suite of small services, each running in its own process and communicating 
with lightweight mechanisms, often an HTTP resource API. 
These services are built around business capabilities and independently deployable 
by fully automated deployment machinery."
```

위 문장에서 `small services, each running in its own process(스스로 돌아갈 수 있는 작은 서비스)` 와 `independently deployable(독립적 배포 가능)` 2가지 특징이 마이크로 서비스를 잘 정의한다. 정리하자면, 마이크로 서비스는 스스로 돌아갈 수 있으며 독립적으로 배포가 가능한 서비스라 할 수 있다.

**마이크로 서비스의 특징**

- 각각의 서비스는 그 크기가 작을 뿐, 서비스 자체는 하나의 모놀리식 아키텍처와 유사한 구조를 갖는다.
- 각각의 서비스는 독립적으로 배포가 가능해야 한다.
- 각각의 서비스는 다른 서비스에 대한 의존성이 작아야 한다.
- 각 서비스는 개별 프로세스로 구동되며, REST API와 같은 가벼운 방식으로 통신되어야 함.

마이크로 서비스의 단위는 하나의 기능, 하나의 프로젝트가 될 수 있으나, 각 팀의 상황에 맞게 경계를 잘 정해서 설계하면 된다.

### 장점

- 배포 관점
    - 서비스 별 개별 배포가 가능하다. (배포 시 전체 서비스의 중단이 없음.)
    - 독립 배포가 가능하므로 개발자의 자율성이 증가한다. (내가 A 기능 다 만들었고, 다른 사람이 B 기능 못 만들었으면, 나는 그냥 놀아도 됨.)
    - 요구 사항을 신속하게 반영하여 빠르게 배포할 수 있다.
- 확장 관점
    - 특정 서비스에 대한 확장성이 용이하다.
    - 클라우드 사용에 적합하다.
- 장애 관점
    - 장애가 전체 서비스로 확장될 가능성이 적다.
    - 부분적 장애에 대한 격리가 수월하다.
- 코드 / 유지 보수 관점
    - 팀 별로 프로젝트가 분리되어 있으므로 코드의 이해도가 증가하고, 그에 따라 유지 보수하기 쉽다.
- 신기술의 적용이 유연하고, 서비스를 polyglot하게 개발 및 운영할 수 있다.
    - polyglot 개발: 여러 프로그래밍 언어, 패러다임 등을 사용

### 단점

- 성능 관점
    - 서비스 간 호출 시 API를 사용하기 때문에 통신 비용 및 지연 시간이 증가한다.
- 데이터 관리 관점
    - 데이터가 여러 서비스에 걸쳐서 분산되므로 한 번에 조회하기 어렵고, 데이터의 정합성 또한 관리하기 어렵다.
- 테스트 / 트랜잭션 관점
    - 단위 테스트는 쉽지만, 통합 테스트 및 End-to-End 테스트 단위로 들어가면 여러 서비스의 API를 검증해야 하므로 시간과 비용이 많이 든다.
    - 각 서비스 별로 데이터베이스가 있으므로 트랜잭션을 구현하기 까다롭다.
- 아키텍처가 다소 복잡하므로 개발 및 관리가 어렵고, 비용이 많이 든다.

### 모놀리식 아키텍처와 비교

**그림**

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F4dacc0e0-8d49-4715-81ef-130adc094f88%2FUntitled.png?table=block&id=51d37930-fc89-40cb-889a-ca81b927042b&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

**표**

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F7887d046-9f81-48bb-b3c9-03be1bf872c9%2FUntitled.png?table=block&id=5ccef255-bb7e-42a9-9da5-41bda060eccc&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### 언제 모놀리식 아키텍처를 사용하고, 언제 MSA 아키텍처를 사용하는가?

초기 시작은 프로젝트의 규모가 작은 경우가 많으므로 모놀리식 아키텍처로 사용하다가 다음 관점들을 비교하여 이득이 된다면 MSA 아키텍처로 전환하는 것이 좋다.

- 비용: MSA 아키텍처를 도입할 경우, 모놀리식 아키텍처에 비해 비용을 얼마나 절감할 수 있는가?
- 개발 생산성: 마이크로 서비스를 요구할 만큼 시스템 복잡도가 높은가? 또는 복잡도를 지나치게 높인 마이크로 서비스가 생산성을 저해하고 있진 않은가?
- 운영: 개발 팀에게 개발과 운영을 동시에 할 만큼 인프라가 준비되어 있는가? 또는 개발 인력이 마이크로 서비스를 관리할 역량이 있는가?
- 배포: 배포를 충분히 자주 하고 있는가? MSA는 빠른 변화에 대응하기 위해 도입하는 것인데, 회사마다 배포 일이 정해져 있고, 배포가 가끔 일어난다면 효율이 떨어진다.

## MSA 아키텍처의 구조

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F02301b8f-43a7-4076-81d9-3939f697a800%2FUntitled.png?table=block&id=2d8d452d-e0b8-4876-a6a3-cef69056d278&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

MSA는 크게 내부 아키텍처와 외부 아키텍처로 구분할 수 있다. 남색 부분은 내부 아키텍처이고, 회색 부분은 외부 아키텍처를 뜻한다.

### 내부 아키텍처

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fc170c5a6-5aba-42d2-b9b3-92bd93e2def2%2FUntitled.png?table=block&id=a99e8518-14c1-4260-9c55-d4dd57a96cbd&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

내부 아키텍처는 내부 서비스와 관련된 아키텍처로, 내부의 서비스를 어떻게 잘 쪼개는 지에 대한 설계라고 생각하면 된다. 내부 아키텍처는 다음을 고려해야 한다.

- 마이크로 서비스를 어떻게 정의할 것인가?
- DB 접근 구조를 어떻게 설계할 것인가?
    - 일부의 비즈니스 트랜잭션은 여러 마이크로 서비스에 걸쳐 있기 때문에, 각 서비스에 연결된 데이터베이스의 정합성을 보장해야 함.
- 마이크로 서비스 내 API를 어떻게 설계할 것인가?
- etc

내부 아키텍처는 각 비즈니스, 서비스, 시스템마다 각각의 특성이 있기 때문에 정해진 것은 없다. 각자 알아서 팀 사정에 맞게 구현하면 된다. 표준이 없기 때문에 MSA를 설계하는 데 가장 어려운 부분이기도 하다.

### 외부 아키텍처

- External Gateway
- Service Mesh
- Container Management
- Backing Services
- Telemetry
- CI/CD Automation

**External Gateway**

외부 게이트웨이는 전체 서비스 외부로부터 들어오는 접근을, 내부 구조를 드러내지 않고 처리하기 위한 요소이다. 사용자 인증과 권한 정책 관리 등을 수행하며, API 게이트웨이가 가장 핵심적인 역할을 한다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fd1213335-70bf-4f8c-b774-ab06472b2bcb%2FUntitled.png?table=block&id=6c95c2a4-880a-4a8b-9954-342247ad18cf&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

API 게이트웨이는 서버 최앞단에 위치하며 모든 API 호출을 받는다. 받은 API 호출을 인증한 후, 적절한 서비스에 메시지를 전달한다.

**Service Mesh**

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fd25559be-e8b4-41f5-b7d6-db331dbaa5c6%2FUntitled.png?table=block&id=8edc1ba1-684d-4c84-9067-7556d086f56a&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

Service Mesh는 마이크로 서비스 구성 요소 간의 네트워크를 제어하는 역할을 한다. 서비스 간에 통신을 하기 위해서는 service discovery, service routing, 트래픽 관리 및 보안 등을 담당하는 요소가 있어야 하는데, 이를 Service Mesh가 모두 수행한다.

**Container Management**

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F77160d60-8029-4be8-a6b6-fad74f4035c7%2FUntitled.png?table=block&id=c72a09a0-5dc3-4e96-acdd-798c336ff4be&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

컨테이너 기반 애플리케이션 운영은 유연성과 자율성을 가지며, 개발자가 손쉽게 접근 및 운영할 수 있는 인프라 관리 기술이므로 MSA 아키텍처가 이에 적합하다고 평가 받는다.

**Backing Service**

Backing 서비스는 애플리케이션이 실행되는 가운데, 네트워크를 통해서 사용할 수 있는 모든 서비스를 말하며, MySQL과 같은 데이터베이스, 캐시 시스템, SMTP 서비스 등 애플리케이션과 통신하는 Attached 자원을 지칭하는 포괄적인 개념이다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F7f3aa915-476f-4eb7-be6c-911946038bcb%2FUntitled.png?table=block&id=df2a66a2-0f5e-4f93-98d9-7bc44d984199&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

대표적인 Backing 서비스의 예시로는 메시지 큐가 있다. MSA에서는 메시지의 송신자와 수신자가 직접 통신하지 않고, 메시지 큐를 활용하여 비동기적으로 통신한다.

만약 메시지 큐를 사용하지 않는 강한 결합 구조의 경우, 여러 서비스를 걸치는 실시간 트랜잭션을 처리할 때, 하나의 서비스가 죽어버린다면 트랜잭션이 끊어지기 때문에 해당 서비스 요청을 보존할 수 없고 큰 에러가 발생하게 된다. 또한 REST 통신으로 트랜잭션 실패에 대한 처리를 구현하는 방법은 굉장히 복잡하다.

**Telemetry**

MSA에서는 상당수의 마이크로 서비스가 분산 환경에서 운영되기 때문에 서비스의 상태를 일일이 모니터링하고 이슈에 대응하는 것이 어렵다. Telemetry는 서비스들을 모니터링하고 서비스 별로 발생하는 이슈에 대응할 수 있도록 환경을 구성하는 역할을 한다.

**CI/CD Automation**

지속적인 통합과 지속적인 배포를 자동화하는 것은 배포가 잦은 MSA 시스템에서 반드시 필요한 요소 중에 하나이다.

## 출처

- [https://velog.io/@tedigom/MSA-제대로-이해하기-1-MSA의-기본-개념-3sk28yrv0e](https://velog.io/@tedigom/MSA-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-1-MSA%EC%9D%98-%EA%B8%B0%EB%B3%B8-%EA%B0%9C%EB%85%90-3sk28yrv0e)
- [https://levelup.gitconnected.com/the-advantages-of-microservices-vs-monolithic-architectures-94ce25ae3fd](https://levelup.gitconnected.com/the-advantages-of-microservices-vs-monolithic-architectures-94ce25ae3fd)
- [https://velog.io/@tedigom/MSA-제대로-이해하기-2-MSA-Outer-Architecure](https://velog.io/@tedigom/MSA-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-2-MSA-Outer-Architecure)
- [https://www.samsungsds.com/kr/insights/msa.html](https://www.samsungsds.com/kr/insights/msa.html)
- [https://www.comworld.co.kr/news/articleView.html?idxno=49710](https://www.comworld.co.kr/news/articleView.html?idxno=49710)

## 예상 면접 질문 및 답변

### 모놀리식 아키텍처란?

모놀리식 아키텍처는 마이크로서비스(MSA) 아키텍처에 반대되는 개념으로, 애플리케이션의 모든 구성 요소가 한 프로젝트에 통합되어 있는 형태를 말한다.

### 모놀리식 아키텍처의 장단점을 설명하라.

- 장점
    - 개발 초기에 단순한 아키텍처 구조로 인해 개발에 용이하다.
    - 어떤 서비스든지 개발되어 있는 환경이 같아서 복잡하지 않다.
    - 배포가 간단하다.
    - 확장성이 쉽다.
        - 로드밸런스를 이용하여 로드 부하를 나눠 가지는 방식으로 진행한다.
    - 쉽게 고가용성 서버 환경을 만들 수 있다.
    - End-to-End 테스트가 용이하다.
- 단점
    - 프로젝트의 규모가 커짐에 따라 애플리케이션 구동 시간이 늘어나고 빌드 및 배포 시간이 길어진다.
    - 조그마한 수정 사항이 있어도 전체를 다시 빌드하고 배포해야 한다.
    - 많은 양의 코드가 몰려 있어서 개발자가 모든 코드를 이해하기 힘들며, 유지 보수가 어렵다.
    - 일부분의 오류가 전체에 영향을 미친다. (장애가 전파된다.)
    - 기술 스택이 한 번 정해지면 바꾸기 어렵다.
    - 전체 애플리케이션 확장은 쉽지만, 부하 분산을 위해 각 컴포넌트를 독립적으로 확장하기 어렵다.

### MSA 아키텍처란?

MSA 아키텍처는 하나의 큰 애플리케이션을 여러 개의 작은 애플리케이션으로 쪼개어 변경과 조합이 가능하도록 만든 형태를 말한다. 작은 레고 블록(microservice) 하나 하나를 붙여 어떠한 큰 결과물을 만드는 레고 놀이를 생각하면 이해하기 쉽다.

### 마이크로 서비스가 무엇인가?

마이크로 서비스는 스스로 돌아갈 수 있으며 독립적으로 배포가 가능한 서비스를 의미한다.

### 마이크로 서비스의 특징을 설명하라.

- 각각의 서비스는 그 크기가 작을 뿐, 서비스 자체는 하나의 모놀리식 아키텍처와 유사한 구조를 갖는다.
- 각각의 서비스는 독립적으로 배포가 가능해야 한다.
- 각각의 서비스는 다른 서비스에 대한 의존성이 작아야 한다.
- 각 서비스는 개별 프로세스로 구동되며, REST API와 같은 가벼운 방식으로 통신되어야 함.

### MSA 아키텍처의 장단점을 설명하라.

- 장점
    - 배포 관점
        - 서비스 별 개별 배포가 가능하다. (배포 시 전체 서비스의 중단이 없음.)
        - 독립 배포가 가능하므로 개발자의 자율성이 증가한다. (내가 A 기능 다 만들었고, 다른 사람이 B 기능 못 만들었으면, 나는 그냥 놀아도 됨.)
        - 요구 사항을 신속하게 반영하여 빠르게 배포할 수 있다.
    - 확장 관점
        - 특정 서비스에 대한 확장성이 용이하다.
        - 클라우드 사용에 적합하다.
    - 장애 관점
        - 장애가 전체 서비스로 확장될 가능성이 적다.
        - 부분적 장애에 대한 격리가 수월하다.
    - 코드 / 유지 보수 관점
        - 팀 별로 프로젝트가 분리되어 있으므로 코드의 이해도가 증가하고, 그에 따라 유지 보수하기 쉽다.
    - 신기술의 적용이 유연하고, 서비스를 polyglot하게 개발 및 운영할 수 있다.
        - polyglot 개발: 여러 프로그래밍 언어, 패러다임 등을 사용
- 단점
    - 성능 관점
        - 서비스 간 호출 시 API를 사용하기 때문에 통신 비용 및 지연 시간이 증가한다.
    - 데이터 관리 관점
        - 데이터가 여러 서비스에 걸쳐서 분산되므로 한 번에 조회하기 어렵고, 데이터의 정합성 또한 관리하기 어렵다.
    - 테스트 / 트랜잭션 관점
        - 단위 테스트는 쉽지만, 통합 테스트 및 End-to-End 테스트 단위로 들어가면 여러 서비스의 API를 검증해야 하므로 시간과 비용이 많이 든다.
        - 각 서비스 별로 데이터베이스가 있으므로 트랜잭션을 구현하기 까다롭다.
    - 아키텍처가 다소 복잡하므로 개발 및 관리가 어렵고, 비용이 많이 든다.

### 언제 모놀리식 아키텍처를 사용하고, 언제 MSA 아키텍처를 사용해야 하는가?

초기 시작은 프로젝트의 규모가 작은 경우가 많으므로 모놀리식 아키텍처로 사용하다가 다음 관점들을 비교하여 이득이 된다면 MSA 아키텍처로 전환하는 것이 좋다.

- 비용: MSA 아키텍처를 도입할 경우, 모놀리식 아키텍처에 비해 비용을 얼마나 절감할 수 있는가?
- 개발 생산성: 마이크로 서비스를 요구할 만큼 시스템 복잡도가 높은가? 또는 복잡도를 지나치게 높인 마이크로 서비스가 생산성을 저해하고 있진 않은가?
- 운영: 개발 팀에게 개발과 운영을 동시에 할 만큼 인프라가 준비되어 있는가? 또는 개발 인력이 마이크로 서비스를 관리할 역량이 있는가?
- 배포: 배포를 충분히 자주 하고 있는가? MSA는 빠른 변화에 대응하기 위해 도입하는 것인데, 회사마다 배포 일이 정해져 있고, 배포가 가끔 일어난다면 효율이 떨어진다.
