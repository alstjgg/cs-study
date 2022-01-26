# Partition count과 replication factor 고르기

Topic을 생성할 때 partition 갯수와 replication factor를 지정해야 한다

- 파티션 갯수
    - 파티션 개수는 변경 가능하나, 수를 감소시키는 것은 불가능하다
    - 개수를 늘릴 경우, key로 보장되었던 순서가 망가진다
- Replication factor
    - 잘못된 수를 지정할 경우, 클러스터의 성능 저하를 일으킬 수 있다

## Partition Count

파티션 마다 견딜 수 있는 throughput은 MB/s로 정해져있다

**파티션이 많으면 ,,,**

- 병렬 처리가 가능해지면서 throughput이 증가한다
- 더 많은 consumer를 하나의 group에서 가질 수 있다
- 더 많은 broker들을 사용할 수 있다 (사용률)
- Zookeeper는 더 많은 leader election을 진행해햐 한다
- 더 많은 파일들이 열려 있어야 한다

**Guideline**

- 6개 이하의 작은 cluster의 경우
    
    → # of clusters * 2
    
- 12개 이상의 큰 cluster의 경우
    
    → # of clusters
    
- Consumer와 producer throughput 고려하기

## Replication Factor

**RF가 크면 ,,,**

- 장애 대응성이 좋다
- 지연이 생길 수 있다
- 많은 저장 공간을 사용해야 한다

**Guidline**

- 최소 2, 보통 3, 최대 4로 한다
- 절대 1은 안됨
- 복제로 인한 성능 저하가 발생한다면, 서버 성능을 개선한다

# APIs

- Admin API
    - Topic, broker 등을 포함한 여러 kafka object를 관리한다
- Producer API
    - 애플리케이션이 kafka topic에 메세지를 publish할 수 있게 한다
- Consumer API
    - 애플리케이션이 kafka topic을 subscribe하여 메세지를 읽어올 수 있게 한다
- Streams API
    - 카프카에 저장된 데이터를 실시간으로 처리하고 분석하기 위해 개발된 클라이언트 라이브러리
    - Aggregation, join, windowing, 등과 같은 고기능성 데이터 처리를 제공한다
    - 데이터를 consume하고 처리한 후 다시 publish할 수 있는 기능을 제공한다
    - Apache Kafka 이외에 dependency에 의존성이 없어서 확장이 용이하다
    - 메세지의 순서를 보장할 수 있고 fault-tolerant 로컬 상태 저장소를 지원한다
- Connect API
    - 외부 시스템과 kafka 간 데이터 소통을 제공

# Use Cases

설계해보기

## Video Analytics

영화나 드라마 스트리밍 사이트

### 구현하고자 하는 기능

- 보다 멈춰도 보던 곳에서 다시 재생하기
- 실시간으로 유저 프로파일 만들기
- 추천 리스트 만들기
- 데이터 저장하기

### 어떻게 구현할까

- Topic: show_position → 보다 멈춰도 보던 곳에서 다시 재생하기
    - Producer: 영상 플레이어
    - Consumer: 영상 플레이어, 추천 시스템
    - Multi-producer topic
    - Partition 갯수는 크게 한다
    - user_id를 key로 사용한다 (사용자 별 정렬 필요)
- Topic: recommendations → 추천 리스트 만들기
    - Producer: 추천 시스템
    - Consumer: 추천 서비스
    - Partition 갯수는 비교적 작게 한다
    - user_id를 key로 사용한다 (사용자 별 정렬 필요)
- 데이터 저장하기
    - Kafka connect API를 사용하여 외부 DB에 저장

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/59bb1a5b-5a44-443d-bd59-89b3e9a28b8c/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-01-26_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9.37.40.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220126%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220126T125025Z&X-Amz-Expires=86400&X-Amz-Signature=a4369780bf2728aefe146869e3f2caff8b4ac4507f8b066da0de7891f58a0d85&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%25202022-01-26%2520%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%25209.37.40.png%22&x-id=GetObject)
## Social Media Platform

### 구현하고자 하는 기능

- 포스팅, 좋아요 누르기, 댓글달기 가능
- 포스트에 대한 좋아요와 댓글 갯수 확인
- 사용자가 많을 것으로 예상됨
- 트랜딩 포스트 확인

### 어떻게 구현할까

Topic은 기본적으로 posts, likes, comments가 필요하다

각 포스트에 대한 좋아요나 댓글 확인은 3 토픽 데이터를 통합하여 생성할 수 있으므로 재생성하여 별도의 토픽에 publish한다

트랜딩 포스트도 마찬가지 데이터를 재생성하여 별도의 토픽으로 publish한다

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/6ea54938-f502-4b95-a4b6-514c9694f1e9/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-01-26_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9.44.12.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220126%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220126T125108Z&X-Amz-Expires=86400&X-Amz-Signature=e803d9ec84d9e15f88f91d50354fe2283e14da7eed9d5127b139efb7143dca91&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%25202022-01-26%2520%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%25209.44.12.png%22&x-id=GetObject)
⇒ CQRS (Command Query Responsibility Segregation, 명령 조회 책임 분리)
