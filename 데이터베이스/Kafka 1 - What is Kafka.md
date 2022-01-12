# Kafka를 왜 개발하였을까

LinkedIn의 Kafka 개발 배경

### 카프카 전 링크드인의 데이터 처리 시스템

![https://media.vlpt.us/images/jaehyeong/post/fc606994-d3b1-4f5b-af61-6ab4562e5f2d/Untitled.png](https://media.vlpt.us/images/jaehyeong/post/fc606994-d3b1-4f5b-af61-6ab4562e5f2d/Untitled.png)

- 각 **파이프라인이 파편화**되고 **시스템 복잡도가 높아** 새로운 시스템을 확장하기 어려움
- 기존 메시징 큐 시스템인 ActiveMQ는 링크드인의 **수많은 트래픽과 데이터를 처리하기에 한계 존재**

**⇒ 새로운 시스템의 개발 필요성**

다음과 같은 **목표**를 가지고 새로운 시스템을 개발

- **프로듀서와 컨슈머의 분리**
- 메시징 시스템과 같이 **영구 메시지 데이터를 여러 컨슈머에게 허용**
- **높은 처리량**을 위한 메시지 최적화
- 데이터가 증가함에 따라 **스케일아웃**이 가능한 시스템

### 카프카 후 링크드인의 데이터 처리 시스템

![https://media.vlpt.us/images/jaehyeong/post/9b33ee27-f4a1-4d66-88cf-8f49c4312e13/Untitled%201.png](https://media.vlpt.us/images/jaehyeong/post/9b33ee27-f4a1-4d66-88cf-8f49c4312e13/Untitled%201.png)

- 모든 이벤트/데이터의 흐름을 **중앙에서 관리**
- 서비스 아키텍처가 기존에 비해 **관리하기에 용이**

# Messaging System

## 비교

### 일반적인 형태의 네트워크 통신의 문제점

![https://media.vlpt.us/images/jaehyeong/post/c6946416-4163-459b-b708-f28927f631ed/Untitled%202.png](https://media.vlpt.us/images/jaehyeong/post/c6946416-4163-459b-b708-f28927f631ed/Untitled%202.png)

- **N:N 연결 →** 클라이언트 갑자기 많아질 경우 느려질 수 있고 이를 대응하기 위한 확장성이 떨어짐
- 특정 클라이언트가 다운되어서 메시지를 받지 못하게 될 경우 메시지가 유실될 수 있음

### Pub/Sub 모델

![https://media.vlpt.us/images/jaehyeong/post/3760527a-9e0d-4cd9-b57a-8b6854562178/Untitled%203.png](https://media.vlpt.us/images/jaehyeong/post/3760527a-9e0d-4cd9-b57a-8b6854562178/Untitled%203.png)

**작동 방식**

- **Producer**가 메시지를 **Consumer**에게 직접 전달하는 것이 아니라, 중간의 메시징 시스템에 전달
- 메시징 시스템의 **교환기**가 메시지의 수신처 ID값을 통해 컨슈머들의 큐에 메시지를 전달 (**push**)
- **Consumer**는 큐를 모니터링하다가 큐에 메시지가 있을 경우 값을 회수

**장점**

- 특정 개체가 수신 불능 상태가 되더라도 메시징 시스템만 살아있다면 **메시지가 유실되지 않음**
- 메시징 시스템으로 연결되어있기 때문에 **확장성이 용이**

**단점**

- 직접 통신하지 않기 때문에 **메시지가 잘 전달되었는지 파악하기 힘듬**
- 중간에 메시징 시스템을 거치기 때문에 **메시지 전달 속도가 빠르지 않음**

## Kafka의 Message System

**Publisher**와 **Subscriber**로 이루어진 **비동기 메시징 전송 방식**

> *a distributed publish-subscribe messaging system*
> 

![https://media.vlpt.us/images/jaehyeong/post/2a914ce1-9c86-4684-8f15-23146eba5fa7/Untitled%204.png](https://media.vlpt.us/images/jaehyeong/post/2a914ce1-9c86-4684-8f15-23146eba5fa7/Untitled%204.png)

### 카프카 아키텍처

- **프로듀서(Producer)**
    - 메시지를 생산하여 브로커의 토픽으로 전달하는 역할
- **브로커(Broker)**
    - 카프카 애플리케이션이 설치되어 있는 서버 또는 노드를 지칭
- **컨슈머(Consumer)**
    - 브로커의 토픽으로부터 저장된 메시지를 전달받는 역할
- **주키퍼(Zookeeper)**
    - 분산 애플리케이션 관리를 위한 코디네이션 시스템
    - 분산된 노드의 정보를 중앙에 집중하고 구성관리, 그룹 네이밍, 동기화 등의 서비스 수행

### 작동방식

- 프로듀서는 새 메시지를 카프카에 전달
- 전달된 메시지는 브로커의 토픽이라는 메시지 구분자에 저장
- 컨슈머는 구독한 토픽에 접근하여 메시지를 가져옴 (pull 방식)
- 메시지라고 불리는 데이터 단위를 보내는 측(Publisher/Producer)에서 메시지 시스템에 메시지를 저장하면 가져가는 측(Subscriber/Consumer)는 데이터를 수신
- 발신자의 메시지에는 수신자가 정해져 있지 않은 상태로 발행(Publish)
- 구독(Subscribe)을 신청한 수신자만이 정해진 메시지를 받음
- 수신자는 발신자 정보가 없어도 메시지 수신이 가능

### 기존 메시징 시스템과 다른 점

- **영속성 - 디스크에 메시지 저장**
    - 기존 메시징 시스템과 가장 큰 특징
    - **기존 메시징 시스템** : Consumer가 메시지를 소비하면 큐에서 바로 메시지를 삭제
    - **카프카** : Consumer가 메시지를 소비하더라도 디스크에 메시지를 일정기간 보관
        
        → 메시지의 손실이 없음
        
- **Multi-Producer, Multi-Consumer**
    - Producer와 Consumer 모두 **하나 이상의 메시지를 주고 받을 수 있음**
- **분산형 스트리밍 플랫폼**
    - 단일 시스템 대비 성능이 우수
    - 시스템 확장이 용이함
    - 일부 노드가 죽더라도 다른 노드가 해당 일을 지속 (**고가용성**)
- **페이지 캐시**
    - 잔여 메모리를 이용해 디스크 Read/Write 를 하지 않고 페이지 캐시를 통한 Read/Write으로 인해 처리속도가 매우 빠름
        
        ![https://media.vlpt.us/images/jaehyeong/post/1bf079fa-d7ae-4019-82e5-4ec66ceb4df8/Untitled%205.png](https://media.vlpt.us/images/jaehyeong/post/1bf079fa-d7ae-4019-82e5-4ec66ceb4df8/Untitled%205.png)
        
- **배치 전송 처리**
    - 메시지를 작은 단위로 묶어 배치 처리를 함으로써 **속도 향상**에 큰 도움을 줌

### 다른 Pub/Sub 메시징 시스템과의 비교

- Kafka
- RabbitMQ
- Google Cloud Pub/Sub

⇒ 모두 비동기 통신을 제공하며, Producer와 Consumer가 분리되어 있음

**RabbitMQ**

- **AMQP**을 위해 개발 되어 다른 AMQP 프로토콜 기반 MQ 등과 **데이터 교환이 수월**
    - AMQP → Advanced Message Queuing Protocol, 클라이언트와 미들웨어간 메시지 교환 개방형 표준 프로토콜
        
        ![https://mblogthumb-phinf.pstatic.net/MjAxNzAyMTdfMzAg/MDAxNDg3MzExMzcyMjk2.jEI4m_D0-Vas3CbnleezAR6Asc9TRvtUqnO2zIzfAuUg.mWzvIYivZ0PxDb1WAqHmo0wnyZG2Nx0H5eCdEEGFw60g.PNG.neos_rtos/image.png?type=w800](https://mblogthumb-phinf.pstatic.net/MjAxNzAyMTdfMzAg/MDAxNDg3MzExMzcyMjk2.jEI4m_D0-Vas3CbnleezAR6Asc9TRvtUqnO2zIzfAuUg.mWzvIYivZ0PxDb1WAqHmo0wnyZG2Nx0H5eCdEEGFw60g.PNG.neos_rtos/image.png?type=w800)
        
    - AMQP 프로토콜 기반 MQ → RabbitMQ, ActiveMQ, ZeroMQ)
- 필요에 따라 **동기/비동기 구현** 가능
- **유연한 라우팅**이 가능(exchanger가 메시지를 적절히 각각의 queue에 분배)하여 관리가 쉽고, 관리UI가 직관적이고 편리
- broker 중심적이며, producer와 consumer간 메시지 전달 보장에 초점을 맞추어 **신뢰성이 높음**
- 20k+/sec 처리 보장

**처리속도 비교**

- Producer의 메시지 배치 처리 성능 비교
    
    ![https://media.vlpt.us/images/jaehyeong/post/408a480e-a818-4525-ba07-c6a9c644ad82/Untitled%2013.png](https://media.vlpt.us/images/jaehyeong/post/408a480e-a818-4525-ba07-c6a9c644ad82/Untitled%2013.png)
    
- Consumer 메시지 수신 처리 성능 비교
    
    ![https://media.vlpt.us/images/jaehyeong/post/6c1a17c9-60b5-4200-b919-7e372bd2e47d/Untitled%2014.png](https://media.vlpt.us/images/jaehyeong/post/6c1a17c9-60b5-4200-b919-7e372bd2e47d/Untitled%2014.png)
    

### Kafka

- 높은 처리량 및 고성능/분산/스케일 아웃이 중요한 경우
- 가용성(장애 대응)이 높아야 하는 경우
- 메시지 전달 보장이 필수적이지 않은 경우
- 메시지 처리 순서가 보장되어야 하는 경우
- 스트리밍 데이터 처리가 필요한 경우
- 메시지 영속성이 필요한 경우

### RabbitMQ

- 빠르고 쉽게 메시지 큐 시스템을 구축하고자 하는 경우 (라우팅 기능이 유연함)
- 메시지 전달 보장이 필수적인 경우
- 메시치 처리 순서가 보장되지 않아도 되는 경우
- 메시지 영속성 X

### Google Pub/Sub

- Kafka와 유사한 아키텍처 (topic 개념)
- 클라우드 서비스로서 별도의 설치나 운영이 필요없음
- 메시지 처리 순서 보장이 안됨(분산형 아키텍처)
- 메시지 영속성 X (저장기간 최대 7일)

# Event Streaming

## What is Event Streaming?

> ... the practice of **capturing data in real-time from event sources** like databases, sensors, mobile devices, cloud services, and software applications **in the form of streams of events**; **storing** these event streams durably for later retrieval; **manipulating**, **processing**, and **reacting** to the event streams **in real-time as well as retrospectively**; and **routing** the event streams to different destination technologies as needed ...
> 

필요한 데이터가 필요한 순간에 필요한 장소에 있도록 보장하는 연속적인 데이터 처리(storing, manipulating, processing, reacting)

예를 들어, ...

- To process **payments and financial transaction**s in real-time, such as in stock exchanges, banks, and insurances.
- To track and monitor cars, trucks, fleets, and shipments in real-time, such as in logistics and the **automotive industry**.
- To continuously capture and analyze **sensor data from IoT devices or other equipment**, such as in factories and wind parks.
- To collect and immediately react to **customer interactions and orders**, such as in retail, the hotel and travel industry, and mobile applications.
- To **monitor patients in hospital care** and **predict changes in condition** to ensure timely treatment in emergencies.

## Kafka, an Event Streaming Platform

Kafka는 event streaming platform이다

이를 위해 3가지 주요 기능을 가지고 있다

1. To **publish** (write) and **subscribe to** (read) streams of events, including continuous import/export of your data from other systems
2. To **store** streams of events durably and reliably for as long as you want
3. To **process** streams of events as they occur or retrospectively

이러한 기능은 distributed, highly scalable, elastic, fault-tolerant이 보장되어 있는 환경에서 구현되어 있다

⇒ 원천 시스템으로부터 **대규모 트랜잭션 데이터**가 발생했을 때 **중간에 데이터를 버퍼링**하면서 **타깃 시스템에 안정적으로 전송**해주는 강력한 기능과 아키텍처를 제공하는 중간 시스템

## Core Capabilities

- **High Throughput**
    
    Deliver messages at network limited throughput using a cluster of machines with latencies as low as 2ms
    
- **Scalable**
    
    Scale production clusters up to a thousand brokers, trillions of messages per day, petabytes of data, hundreds of thousands of partitions. Elastically expand and contract storage and processing
    
- **Permanent Storage**
    
    Store streams of data safely in a distributed, durable, fault-tolerant cluster
    
- **High Availability**
    
    Stretch clusters efficiently over availability zones or connect separate clusters across geographic regions
    

# 출처

- [https://kerpect.tistory.com/63?category=874039](https://kerpect.tistory.com/63?category=874039)
- [https://kafka.apache.org/](https://kafka.apache.org/)
- [https://www.tibco.com/ko/reference-center/what-is-apache-kafka](https://www.tibco.com/ko/reference-center/what-is-apache-kafka)
- [https://engineering.linecorp.com/ko/blog/how-to-use-kafka-in-line-1/](https://engineering.linecorp.com/ko/blog/how-to-use-kafka-in-line-1/)
- [https://velog.io/@jaehyeong/Apache-Kafka아파치-카프카란-무엇인가](https://velog.io/@jaehyeong/Apache-Kafka%EC%95%84%ED%8C%8C%EC%B9%98-%EC%B9%B4%ED%94%84%EC%B9%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)
- [https://galid1.tistory.com/793](https://galid1.tistory.com/793)
- [https://m.blog.naver.com/neos_rtos/220937824768](https://m.blog.naver.com/neos_rtos/220937824768)
