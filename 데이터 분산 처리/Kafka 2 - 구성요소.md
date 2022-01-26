# Previously on...

**Kafka는 무엇인가**

- Publisher와 Subscriber로 이루어진 pub/sub model을 사용하는 메세징 시트템
- 실시간 메세지 처리(event streaming) 플랫폼
  
![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/c1001740-2404-4b73-b438-994b98329a0b/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-01-16_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.17.54.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220126%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220126T125131Z&X-Amz-Expires=86400&X-Amz-Signature=5e8b80ba40ddd81c3a9aeac5c507ca035e658a7929c3d6752e5fd508a9522faa&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%25202022-01-16%2520%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%25204.17.54.png%22&x-id=GetObject)

- 원천 시스템으로부터 **대규모 트랜잭션 데이터**가 발생했을 때 **중간에 데이터를 버퍼링**하면서 **타깃 시스템에 안정적으로 전송**해주는 강력한 기능과 아키텍처를 제공하는 미들웨어

**Kafka를 왜 쓰는가**

- 고성능
- 확장성
- 영속성
- 고기능성

**Kafka는 어디에 쓰이는가**

- 실시간 데이터 처리가 필요한 시스템
- Netflix 드라마 추천
- Uber 배달 예상 시간 예측
- LinkedIn 스팸 처리 및 추천

# 목차

# Topic & Partitions & Offsets

## 개념

- **Topic**
    - 데이터 스트림(A particular stream of data) → 메시지를 논리적으로 묶은 개념
    - RDB의 테이블, 파일 시스템의 폴더와 유사한 개념
    - 지정된 데이터 스트림(일련의 이벤트/레코드/메시지)에 대한 관심을 표시하는 데 사용되는 주소 지정 가능한 추상화
    - Topic에 게시*(publish)*하거나  Topic을 구독*(subscribe)*할 수 있다
    - Multi-producer and Multi-subscriber
- **Partition**
    - 토픽을 구성하는 데이터 저장소이며 메시지가 저장되는 위치
    - Topic은 Partition이라는 일련의 순서 대기열로 세분화된다
    - Topic을 생성할 때 몇 개의 Partition을 가지는지 명시한다 (이후 변경 가능)
    - 각 Partition 내의 데이터(이벤트, 레코드, 메세지)는 incremental id(순차 아이디)를 가지게 된다 ⇒ **offset**
- **Offset**
    - Partition은 Offset으로 분리된다
        
        → Offset은 메세지가 partition에 저장되는 위치 (partition 내 순서 보장)
        
        ![https://media.vlpt.us/images/jaehyeong/post/425f841c-bdb0-4ee3-a501-7943e9178217/Untitled%208.png](https://media.vlpt.us/images/jaehyeong/post/425f841c-bdb0-4ee3-a501-7943e9178217/Untitled%208.png)
        
    - 하나의 Partition에 속해 있기 때문에 해당 파티션 내에서만 의미가 있다
- **Event**
    - 실제 발생(publish)하고 저장되는 데이터 *(= 이벤트 = 레코드 = 메세지)*
    - Topic이 파일 시스템 내의 폴더라면, event는 그 폴더 안에 저장되어 있는 파일이다
    - 일정 기간 동안만 저장된다(default: 1 week) → 지속적으로 데이터를 조회할 수 있다
    - 업데이트/변경이 불가능하다
    - 특정 key가 주어지지 않으면 임의의 partition에 저장(append)되며, 같은 key를 가진 이벤트는 같은 partition에 저장된다

⇒ Topic 2, Partition 0, Offset 9 형태로 표현 가능

## Partition은 **많을 수록 좋을까**

Partition을 통해 다수의 producer가 publish하는 메세지에 대한 처리 성능도 개선할 수 있고 메세지 발생 순서도 보장할 수 있으며 분산 환경도 구현할 수 있다. 그렇다면 partition은 무조건 많을 수록 좋은 것 아닐까?

- **파일 핸들러의 낭비가 존재**
    - 각 파티션은 브로커의 디렉토리와 매핑되고 저장되는 데이터마다 2개의 파일(인덱스, 실제 파일)이 있기 때문에 너무 많은 파일 핸들이 생길 경우 리소스 낭비가 생김
- **장애 복구 시간이 증가할 수 있음**
    - 카프카는 리플리케이션(Replication)을 지원하고, 이를 통해 지속적으로 리더 파티션을 팔로워 파티션으로 리플리케이션을 하게됨
    - 하지만 파티션 수가 너무 많을 경우 리플리케이션 수행이 느려져 장애복구시간이 증가할 수 있음
- **추후 파티션 수를 줄이는 것이 불가능**
    - 카프카에서 파티션 수를 늘리는 것은 아무때나 가능하지만 파티션 수를 줄이는 방법은 제공하지 않음. 만약 줄이고 싶다면 토픽 자체를 삭제하는 것 말고는 방법이 없음

## 메시지순서

- 프로슈머가 메시지를 보낼 때, 파티션이 여러개인 경우 메시지는 각각의 파티션으로 순차적으로 배분 (Round-robin 방식)
- 메시지의 순서는 오로지 동일 파티션 내 오프셋을 기준으로만 보장
- 여러개의 파티션을 사용할 경우 동일 파티션 내에서는 순서가 보장되지만, 파티션과 파티션 사이에서는 순서를 보장하지 못하기 때문에 전체 메시지를 출력할 경우 순서가 섞일 수 있음
- 전체 메시지의 순서를 보장하고 싶은 경우 partition을 1개로만 설정해야 함
    - 하지만 파티션이 하나이므로 분산 처리가 불가능

## 예시

- Topic with 4 partitions
    
    ![https://kafka.apache.org/images/streams-and-tables-p1_p4.png](https://kafka.apache.org/images/streams-and-tables-p1_p4.png)
    
    - 해당 topic은 4개의 partition으로 이루어져있다
    - 2개의 producer가 이 topic에 publish를 하고 있다
    - 같은 키(색으로 표현)를 가진 이벤트는 같은 partition에 저장된다
- ***truck_gps* topic**
    - 트럭 수천대의 위치 정보가 실시간 스트림 형태로 들어온다
    - 각 트럭은 고유 truck_id, 그리고 현재 위치 정보를 Kafka로 보낸다 *(producer)*
    
      ![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/8d716c27-6fdf-4ed6-9337-94dcbb0dac10/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-01-16_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.38.09.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220117%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220117T120010Z&X-Amz-Expires=86400&X-Amz-Signature=a3fc71b6c5b440315d30cfd2a14bd6155d213ba66237747ca92c8286a1f16ea3&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%25202022-01-16%2520%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%25204.38.09.png%22&x-id=GetObject)
    
    - 해당 topic을 생성할 때 partition 갯수는 10개로 하기로 했다 *(임의 필수 지정)*
    - 이를 이용해 각 트럭이 어디 위치해 있는지 보여주는 대시보드와 알림 서비스를 개발하고자 한다 *(consumer)*

# Brokers & Topics

메세지/레코드를 저장하는 partition과 topic은 어디에 저장되는 것일까?

## 개념

- **Broker**
    - Kafka Server
    - 고유 id(정수 형태)를 가지고 있다
    - 특정 topic의 partition만을 보관한다
        
        (일부 데이터만 보관 → **partition을 통한 분산환경 구현**)
        
- **Cluster**
    - 다수의 broker로 이루어짐 (3~100개 정도)
    - 확장성과 장애 대응성을 보장한다
    - 하나의 broker와 연결되면 전체 cluster와 연결된다
        
        **→ bootstrap broker**
        

## 예시

- **Topic-A (3 Partitions) & Topic-B (2 Partitions)**
    
  ![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/f585ae90-7099-4b2f-b792-9f54c1831b0d/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-01-16_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.47.47.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220126%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220126T125233Z&X-Amz-Expires=86400&X-Amz-Signature=6974c782cc3881b550257b54aef105d7c5de16430b8edd134c37f721c55e5427&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%25202022-01-16%2520%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%25204.47.47.png%22&x-id=GetObject)
# Topic Replication

데이터를 복제하여 저정한다

## 예시

- **Topic-A (2 Partition, Replication Factor=2)**
    
  ![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/82efcb34-ab54-421d-a1c8-2386b162287e/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-01-16_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_5.20.34.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220126%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220126T125248Z&X-Amz-Expires=86400&X-Amz-Signature=c57b2bfec9f116641f4ad4ed090730820fd62b19d5a5fa122b76501588ac6fe2&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%25202022-01-16%2520%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%25205.20.34.png%22&x-id=GetObject)    
    - Broker 102가 실패하여도 같은 데이터가 Broker 101과 Broker 103에 저장되어 있기 때문에 문제 없다
    - 결론적으로 데이터는 손실되지 않고 서비스는 지속된다

## 개념

- **Leader & Follower**
    - 하나의 partition은 여러개의 broker에 replicate 되어 있는데, 이 때 **partition은 한번에 하나의 broker만을 leader로 가질 수 있다**
    - 원본 파티션이 leader가 되고, 복제 파티션은 follower가 된다
    - **Leader**만이 해당 partition에 대해 데이터를 receive하고 serve할 수 있다
    - **Follower**는  leader의 데이터를 동기화(synchronize)한다
        
      ![](https://media.vlpt.us/images/jaehyeong/post/c9d43455-6b4a-4ab4-bd33-2202d4809698/img.jpg)
      
        **⇒ 하나의 partition은 하나의 leader와 다수의 ISR를 가진다**
        
    - 만약 리더 파티션이 있는 브로커가 다운될 경우, 복제 파티션을 가진 브로커의 팔로워 파티션이 새로운 리더가 되어 정상적으로 프로듀서의 요청을 처리한다
- **ISR(In Sync Replica)**
    - 리더와 팔로워로 이루어진 리플리케이션 그룹
    - 리플리케이션 그룹 내 동기화 및 신뢰성 유지
    - 팔로워는 Read/Write 권한이 없고 오로지 리더로부터 데이터를 복제하기 때문에 특정 팔로워가 다운되어서 리플리케이션을 못할 경우 동기화 문제 발생
        
        → 설정된 일정주기만큼 요청이 오지 않는 팔로워는 문제가 있다고 판단하여 ISR 그룹에서 추방된다
        
    
    ![https://media.vlpt.us/images/jaehyeong/post/9760a3c4-7601-44ba-b99b-b25279e4cbdd/Untitled%209.png](https://media.vlpt.us/images/jaehyeong/post/9760a3c4-7601-44ba-b99b-b25279e4cbdd/Untitled%209.png)
    

## 왜?

- 분산환경 구현
    
    → 고가용성
    
- 장애 대응 (Fault-Tolerance) & 데이터 유실 예방
    
    → Broker 하나가 실패해도 다른 broker가 데이터를 보내주면 된다
    

# Producer & Message Keys

## 개념

- **Producers**
    - Kafka로 이벤트, 레코드, 메세지를 publish하는 client applications
        
        → Broker의 특정 topic에 publish한다
        
    - 어떤 broker의 어떤 partition에 데이터를 전송할지 알고 있다 (key를 지정해주지 않아도 알아서 publish 진행)
    
  ![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/bd7ad36a-9c1c-429d-bba9-5ca717da22b8/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-01-16_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_5.40.49.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220126%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220126T125307Z&X-Amz-Expires=86400&X-Amz-Signature=41da40e5ded280f7aa525fb6c13b9bf5e2ff2ff9a1f4488f628f2816c7b2e99a&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%25202022-01-16%2520%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%25205.40.49.png%22&x-id=GetObject)    
- **Acknowlegment**
    - Producer는 Data 전송 이후 메세지를 받을지 정할 수 있다
    - **acks=0** : 기다리지 않는다
        
        → 데이터 유실의 위험성 존재
        
    - **acks=1 (default)** : leader acknowledgment를 기다린다
        
        → 데이터 유실의 가능성 존재
        
    - **acks=all** : leader와 follower acknowledgment를 기다린다
        
        → 데이터 유실 없음
        
- **Message Key**
    - Producer는 data와 함께 보낼 key를 지정할 수 있다
    - **key=null** : round-robin으로 전송된다
        
        → *ex) Broker 101, Broker 102, Broker 103, ...*
        
    - key를 지정하면 같은 key를 가진 데이터는 모두 같은 partition으로 전송된다
        
        → 어떤 partition으로 갈지 정하는 것이 아니라, 같은 partition으로 보내져서 해당 메세지들의 순서를 보장하는 것이다
        
    - 메세지의 순서를 정해야할 때 사용한다

# Consumer & Consumer Group

## 개념

- **Consumer**
    - Topic을 구독하여 데이터를 수신/소비한다
    - 각 consumer는 어떤 broker에서 데이터를 읽어올지 알고 있다
    - 데이터는 각 partition 내의  순서대로(offset 순서대로) 읽어온다
    - Partition 간의 순서는 보장되어 있지 않다
- **Consumer Group**
    - Consumer는 consumer group을 이루어 데이터를 읽는다
    - 하나의 application 을 표현한다
    - Group 내의 consumer들은 각기 다른 partition에서 데이터를 읽는다
        
        → Group 내 consumer 수가 partition 수보다 많다면 inactive consumer가 생긴다
        
        → 각자 어떤 partition을 읽을지는 정해져 있음
        

## 예시

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/76a1b894-6e11-41cc-8b3f-1e8185d9eace/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-01-16_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_6.09.40.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220126%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220126T125322Z&X-Amz-Expires=86400&X-Amz-Signature=7d469f525718670ccb010b5abb290349239a71136107eb4eb214703503a8ab3f&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%25202022-01-16%2520%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%25206.09.40.png%22&x-id=GetObject)
- Topic-A를 partition 3로 지정
- consumer-group-application-1
    - 2개의 consumer 존재
    - 1개, 2개의 partition에서 각각 데이터를 읽어옴
- consumer-group-application-2
    - 3개의 consumer 존재
    - 각각 1개의 partition에서 데이터를 읽어옴
- consumer-group-application-3
    - 1개의 consumer 존재
    - 모든 partition에서 데이터를 읽어옴
- 만약 consumer가 4개라면 하나의 consumer는 비활성화 상태가 된다
    
    → 보통은 사용하지 않는 경우 *(보통 consumer 수와 partition 수를 맞춰준다)*
    
    → 데이터를 읽어오던 consumer 하나가 실패하면 비활성화 상태였던 consumer가 대신 데이터를 읽어온다
    

## Consumer Group, 왜 필요할까?

- Consumer가 메시지를 소비하는 시간보다 Producer가 메시지를 전달하는 속도가 더 빨라서 메시지가 점점 쌓일 경우를 대비하여

![https://media.vlpt.us/images/jaehyeong/post/f86a8113-01cc-4462-9508-18e58f3ed3bb/Untitled%2010.png](https://media.vlpt.us/images/jaehyeong/post/f86a8113-01cc-4462-9508-18e58f3ed3bb/Untitled%2010.png)

![https://media.vlpt.us/images/jaehyeong/post/b1da98e2-4f97-4889-9a66-2ed1549d4617/Untitled%2011.png](https://media.vlpt.us/images/jaehyeong/post/b1da98e2-4f97-4889-9a66-2ed1549d4617/Untitled%2011.png)

- 큐에서 메시지를 가져가도 사라지지 않기 때문에 여러 consumer group이 동일 topic에 붙을 수 있음

# Consumer Offsets

## 개념

- **Consumer Offsets**
    - Kafka는 consumer가 읽었던 partition offset을 저장한다
        
        *→ checkpoints, bookmarks 같은 개념*
        
    - “__consumer_offset”라는 이름의 topic에 실시간으로 commit된다
    - 읽어온 데이터에 대한 처리를 끝내면 commit 된다
        
        → Consumer가 다운되어도 가장 최근에 작업하고 있던 offset에서 시작할 수 있다
        
- **Delivery Semantics for Consumers**
    - Consumer가 언제 offset을 commit할지 지정할 수 있다 (3가지 방식)
    1. **At most once**
        
        메세지를 수신하자 마자 commit
        
        → 제대로 처리를 못하면 메세지는 유실된다
        
    2. **At least once** (가장 선호됨)
        
        메세지를 처리하면 commit
        
        → 처리를 못하면 다시 메세지를 받아온다
        
        → 같은 메세지를 여러번 처리하게 될 수도 있다 (duplicate processing)
        
        이런 상황이 발생해도 괜찮도록 시스템을 설계해야한다 (make processing idempotent) 
        
    3. **Exactly Once**
        
        Kafka API를 사용하여 Kafka→Kafka 인 경우에만 사용 가능
        
        Kafka→외부시스템인 경우에는 idempotent consumer이어야 가능
        

# Kafka Broker Discovery

Producer는 어떤 broker에 publish해야할지 알고 있고, consumer도 어떤 broker에서 데이터를 읽어와야하는지 알고 있고, 모두 broker 실패 시 어떤 broker를 다음으로 선택할지 알고 있다고 하였다.

이렇게 broker를 찾는 것은 kafka 뒷 단에서 자동으로 이루어져 사용자가 신경쓰지 않아도 되는데, 이 과정을 kafka broker discovery라고 부른다.

## Bootstrap Server

Cluster 내의 하나의 broker와 연결이 된다면 모든 broker와 연결이 가능한데, 이 때 해당 broker를 bootstrap server라고 부른다

- 모든 kafka broker는 bootstrap server이다
- 모든 kafka broker는 cluster 내의 모든 broker, topic, partition에 대해 알고 있다
    
    → Metadata
    

## Broker Delivery (example)

- 5개의 broker로 이루어진 kafka cluster가 있다
- Kafka client가 이 cluster와 연결하고자 한다
    - kafka client → producer or consumer

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/3eea90c7-05ab-4e39-88ea-b65aaba22cd6/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-01-17_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_7.56.03.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220117%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220117T120133Z&X-Amz-Expires=86400&X-Amz-Signature=760bfb91fce60ccaf4afeaa93887ae3d2ed49489a046d183f6d8302ecf59145d&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%25202022-01-17%2520%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%25207.56.03.png%22&x-id=GetObject)

1. **Connection + Metadata request**
    - Broker 101과 연결한다
    - 다른 broker와 연결해도 상관없다
    - 연결이 완료되면 metadata를 요청한다
2. **List of all brokers**
    - Cluster 내 broker 고유 id, ip, 등등을 전송한다
3. **Can connect to the needed brokers**
    - consume, publish 할 때 모든 broker와 연결할 수 있다

# Zookeeper

## 역할

- 모든 broker를 관리한다
- Partition내의 leader를 선정하는 leader election을 돕는다
- 변경 사항에 대한 notification을  Kafka에게 보낸다
    
    → 새로운 topic, broker 실패, 등
    

**⇒ Kafka는 zookeeper 없이 일할 수 없다**

## 특징

- 홀수 개의 server를 관리해야 한다
- Zookeeper에는 leader 하나와 followers가 있다
    
    → leader : write
    
    → follower : read
    
- Zookeeper는 consumer offset을 저장하지 않는다
    
    → consumer, producer에 독립적이다
    

## 구조(example)

- 3개의 서버를 가진 zookeeper *(홀수)*
- 5개의 broker를 관리

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/3eea90c7-05ab-4e39-88ea-b65aaba22cd6/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-01-17_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_7.56.03.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220126%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220126T125403Z&X-Amz-Expires=86400&X-Amz-Signature=789b358030b6291fd596db16b9500c9b50a20b8740e92f5712311601a269b55e&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%25202022-01-17%2520%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%25207.56.03.png%22&x-id=GetObject0)
- Kafka cluster와 zookeeper cluster가 연결되어 있다

# 중요 포인트

- 메세지는 보내진 순서대로 topic의 partition에 append된다
- Consumer는 topic의 partition에 저장되어 있는 순서대로 메세지를 읽는다
- Replication factor가 N이라면, producer와 consumer는 broker가 N-1개까지 다운되어도 괜찮다
- Replication factor의 default는 3이다
    - 한 개는 유지 보수를 위해 내리고,
    - 한 개는 예기치 못하게 다운되어도,
    - 한 개는 멀쩡하게 작동한다
- 새로운 partition이 생성되지 않는 한, 같은 key를 가진 메세지는 같은 parition에 들어간다

# 결론

![https://media.vlpt.us/images/jaehyeong/post/96498ed8-7e67-4f2e-a53b-15b4a4f62db8/Untitled%206.png](https://media.vlpt.us/images/jaehyeong/post/96498ed8-7e67-4f2e-a53b-15b4a4f62db8/Untitled%206.png)

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/baac9280-c802-4614-aaa5-cc4632e44f8c/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-01-17_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_8.18.23.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220117%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220117T115925Z&X-Amz-Expires=86400&X-Amz-Signature=9ee2f30aac413944964c0f7580822cb770caeee17de3c5c54354bcda600a8d7a&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%25202022-01-17%2520%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%25208.18.23.png%22&x-id=GetObject)

# 출처

- [https://kerpect.tistory.com/63?category=874039](https://kerpect.tistory.com/63?category=874039)
- [https://kafka.apache.org/](https://kafka.apache.org/)
- [https://www.tibco.com/ko/reference-center/what-is-apache-kafka](https://www.tibco.com/ko/reference-center/what-is-apache-kafka)
- [https://engineering.linecorp.com/ko/blog/how-to-use-kafka-in-line-1/](https://engineering.linecorp.com/ko/blog/how-to-use-kafka-in-line-1/)
- [https://velog.io/@jaehyeong/Apache-Kafka아파치-카프카란-무엇인가](https://velog.io/@jaehyeong/Apache-Kafka%EC%95%84%ED%8C%8C%EC%B9%98-%EC%B9%B4%ED%94%84%EC%B9%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)
- [https://galid1.tistory.com/793](https://galid1.tistory.com/793)
- [https://m.blog.naver.com/neos_rtos/220937824768](https://m.blog.naver.com/neos_rtos/220937824768)
- O’REILLY Apache Kafka Series - Learn Apache Kafka for Beginners
