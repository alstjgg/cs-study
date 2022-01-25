## Cache

### Cache의 개념

Cache란 나중에 요청할 결과를 미리 저장해둔 후 빠르게 서비스해 주는 것을 의미한다. 즉, 미리 결과를 저장하고 나중에 요청이 오면 그 요청에 대해서 DB 또는 API를 참조하지 않고 Cache를 접근하여 요청을 처리하는 기법이다. 이러한 cache가 나온 배경에는 파레토 법칙이 있다.

파레토 법칙이란 80%의 결과는 20%의 원인으로 인해 발생한다는 뜻이다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fee96f480-0951-47d9-b55c-c1ae7258de16%2FUntitled.png?table=block&id=9b125398-33e2-4e3c-b6f7-c01c1732eb28&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

즉, 캐시는 모든 결과를 캐싱할 필요가 없으며 서비스를 할 때 많이 사용되는 20%만 캐싱함으로써 전체적으로 효율을 끌어올릴 수 있다.

### 현실적으로 Cache를 접하게 되는 순간

서비스를 처음 운영할 때는 WEB-WAS-DB 정도로 작게 인프라를 구축하는데, 사용자가 늘어나면 DB에 무리가 가기 시작한다. DB는 데이터를 물리 디스크에 직접 쓰기 때문에 서버에 문제가 발생해도 데이터가 손실되지는 않지만, 매 트랜잭션마다 디스크에 접근해야하므로 부하가 많아지면 성능이 떨어진다.

그래서 사용자가 늘어나면 DB를 스케일 인 또는 스케일 아웃하는 방식 외에도 캐시 서버를 검토하게 된다.

### Cache 사용 구조

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F1d0c7463-62f1-4267-a87c-8a2d45d42398%2FUntitled.png?table=block&id=f9147c16-54f2-4823-8d04-1ba3e53c2ec0&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

1. 클라이언트로부터 요청을 받는다.
2. Cache와 작업을 한다.
3. 실제 DB와 작업한다.
4. 다시 Cache와 작업한다.

클라이언트가 웹 서버에 요청을 보내면, 웹 서버는 데이터를 DB에서 가져 오기 전에 캐시에 데이터가 있는지 확인하고, 있다면 바로 클라이언트에게 저장된 데이터를 반환한다. 이를 Cache Hit라고 한다.

반대로 캐시 서버에 데이터가 없으면 DB에 데이터를 요청하여 원하는 데이터를 조회한 후 그 데이터를 클라이언트에게 제공하는데, 이를 Cache Miss라고 한다.

위 flow에서 캐시를 어떻게 사용하느냐에 따라서 look aside cache와 write back으로 나뉜다.

**Look Aside Cache (Lazy Loading)**

1. 캐시에 데이터 존재 유무 확인
2. 데이터가 있다면 캐시의 데이터 사용
3. 데이터가 없다면 캐시의 실제 DB 데이터 사용
4. DB에서 가져 온 데이터를 캐시에 저장

look aside cache는 캐시를 한 번 접근하여 데이터가 있는지 판단한 후, 있다면 캐시의 데이터를 사용하고 없으면 실제 DB 또는 API를 호출한다. 대부분의 캐시를 사용한 개발이 해당 프로세스를 따른다.

**Write Back**

1. 모든 데이터를 캐시에 저장
2. 캐시의 데이터를 일정 주기마다 DB에 한꺼번에 저장 (배치)
3. DB에 저장한 데이터를 캐시에서 제거

write back은 주로 쓰기 작업이 굉장히 많아서, INSERT 쿼리를 일일이 날리지 않고 한꺼번에 배치 처리를 하기 위해 사용한다. 예를 들어 영어 듣기 평가를 온라인으로 진행하는 서비스가 있을 때, 여러 학생이 동시에 제출 버튼을 누르면서 DB에 갑작스럽게 엄청난 쓰기 요청이 몰리게 되면 DB 서버가 죽을 수도 있다. 이때 write back 기반의 캐시를 사용하면 캐시 메모리에 데이터를 저장해 놓고, 이후 DB 디스크에 업데이트 해 주면 안전하게 쓰기 작업을 이행할 수 있는 것이다.

DB에서 디스크를 접근하는 횟수가 줄어들기 때문에 성능 향상을 기대할 수 있지만, DB에 데이터를 저장하기 전에 캐시 서버가 죽으면 데이터가 유실된다는 문제점이 있다.

## Memcached

### Memcached의 개념

Memcached는 무료로 사용할 수 있는 오픈 소스이며 분산 메모리 캐싱 시스템이다. DB의 부하를 줄여 동적 웹 애플리케이션의 속도 개선을 위해 사용하며, DB나 API 호출 또는 렌더링 등으로부터 받아오는 결과 데이터를 key - value 형태로 메모리에 저장한다.

### 장점

시스템의 사용되지 않는 일부 메모리를 활용할 수 있어 남는 자원을 효율적으로 사용하여 성능을 향상시킬 수 있다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fbf68a3cb-e72d-4709-a02e-c0967c5b7347%2FUntitled.png?table=block&id=ea3ed5ee-e8f3-4c9a-a258-a252f4c82154&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

초창기의 캐시 시스템은 [그림 1]과 같이 각 노드가 완전히 독립적으로 운영되어 데이터를 조회하거나 저장 시 어느 서버를 이용할지 관리해야 하고, 용량의 제한으로 인해 비생산적이며 자원 낭비적인 시스템으로 구성되었다.

Memcached는 이러한 제약 사항을 해결하기 위해 [그림 2]와 같이 consistent hash 알고리즘을 사용하여 물리적인 별도의 캐시 서버를 로직 상 하나의 서버로 보고 사용할 수 있도록 하였다. 즉, 개발자는 서버가 몇 대든 상관없이 한 개의 객체만을 활용하여 저장 및 조회할 수 있으므로 능률적이고 대용량의 캐시 시스템을 갖게 되는 것이다. 또한, 기업의 입장에서는 오래된 저사양 서버의 남는 메모리를 활용할 수 있기 때문에 비용 면에서 효율적일 수 있다.

### 단점

인 메모리 기반의 시스템이므로 재부팅 시 데이터가 소멸하고, 이로 인해 영구적인 저장용 시스템으로 활용할 수 없다는 문제가 있다. 만약 영구 저장이 필요하다면 해당 데이터를 DB에 저장해 두고, 재부팅 시 DB로부터 데이터를 받아야 한다.

## Redis

### Redis의 개념

Redis는 오픈 소스로서 NoSQL로 분류되기도 하고, Memcached와 같이 인 메모리 솔루션으로 분류되기도 한다. 성능은 Memcached에 버금가면서 다양한 데이터 구조체를 지원함으로써 DB, Cache, Message Queue, Shared Memory 용도로 사용될 수 있다.

한편, Redis는 Remote Dictionary Server의 약자로 외부에서 사용 가능한 Key-Value 쌍의 해시 맵 형태의 서버라고 생각할 수 있다. 그래서 별도의 쿼리 없이 Key를 통해 빠르게 결과를 가져올 수 있다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F7542f7b0-bc80-416e-8039-9f2cdc4b17c3%2FUntitled.png?table=block&id=e60b1004-57ee-46ed-801b-2ba383086d7f&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

또한, 디스크에 데이터를 쓰는 구조가 아니라 메모리에서 데이터를 처리하기 때문에 작업 속도가 상당히 빠르다.

개인적으로 Redis를 한 줄로 정의하라고 하면, `Redis는 고성능 키-값 저장소로서 String, list, hash, set, sorted set 등의 자료 구조를 지원하는 NoSQL` 라고 할 것 같다.

### Redis의 특징

- 영속성을 지원하는 인 메모리 데이터 저장소
- 다양한 자료 구조를 지원함.
- 싱글 스레드 방식으로 인해 연산을 원자적으로 수행이 가능함.
- 읽기 성능 증대를 위한 서버 측 리플리케이션을 지원
- 쓰기 성능 증대를 위한 클라이언트 측 샤딩 지원
- 다양한 서비스에서 사용되며 검증된 기술

### Redis의 영속성

Redis는 영속성을 보장하기 위해 데이터를 디스크에 저장할 수 있다. 서버가 내려가더라도 디스크에 저장된 데이터를 읽어서 메모리에 로딩한다. 데이터를 디스크에 저장하는 방식은 크게 두 가지가 있다.

- RDB(Snapshotting) 방식
    - 순간적으로 메모리에 있는 내용 전체를 디스크에 옮겨 담는 방식
- AOF(Append On File) 방식
    - Redis의 모든 write/update 연산 자체를 모두 log 파일에 기록하는 형태

### Redis의 컬렉션

위 개념만 들으면 Redis는 단순히 데이터를 메모리에 저장하여 빠른 DB이고, Memcached처럼 단순한 Key-Value 쌍이 아니고 다양한 데이터 구조체를 지원한다. 이를 컬렉션이라고 부르는데 아래 그림을 살펴 보자.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F6416f30d-bcfb-4f84-bdec-82fb69a73f43%2FUntitled.png?table=block&id=d56c51ce-1c17-47f7-b92f-624e875adf08&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

Key가 될 수 있는 데이터 구조체가 다양한 것을 알 수 있다. 이렇게 다양한 자료 구조를 지원하게 되면 개발의 편의성이 좋아지고 난이도가 낮아진다는 장점이 있다. 

### 싱글 스레드를 사용하는 Redis

Redis는 싱글 스레드를 사용하므로 연산을 원자적으로 처리하여 Race Condition이 거의 발생하지 않는다. 예를 들어, 친구 리스트의 친구를 추가하는 연산을 시도해 보자. 아래와 같이 정상적인 상황에서는 유저 각각의 트랜잭션이 순서대로 잘 행해지고 있으므로 문제가 없다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F3991fe95-6548-4a1e-9ace-f4b4fa569fb4%2FUntitled.png?table=block&id=49f133cc-a8fd-470c-be63-849ba31a75b7&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

그러나 동시에 친구 리스트에 B, C를 추가한다고 하면 어떨까?

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F0d1c3851-0308-4eae-8b93-e772938a0d6d%2FUntitled.png?table=block&id=c5bde51d-f5bb-4a13-a2dc-65bdd3072c51&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

두 트랜잭션이 동일한 최종 상태인 A를 자신의 메모리로 읽어 들이고, 그 상태에서 각자 B 또는 C를 추가하게되면 최종 상태가 (A, B) 혹은 (A, C)가 된다. (A, B) 혹은 (A, C)라고 한 이유는 컨텍스트 스위칭에 따라 두 트랜잭션 중 누가 먼저 끝날 지 예측할 수 없기 때문이다. 물론 이러한 Race Condition을 해결하기 위해 격리 수준 등 여러 가지 기법이 있지만, Redis는 싱글 스레드를 사용하므로 하나의 트랜잭션은 하나의 명령만 실행할 수 있으므로 다수의 Race Condition을 해결할 수 있다. (모든이 아닌 다수라고 한 이유는 더블 클릭 이슈는 싱글 스레드만으로 해결 못함)

## 출처

- [https://brunch.co.kr/@jehovah/20#comment](https://brunch.co.kr/@jehovah/20#comment)
- [https://myblog.opendocs.co.kr/archives/591](https://myblog.opendocs.co.kr/archives/591)
- [https://brownbears.tistory.com/43](https://brownbears.tistory.com/43)
- [https://sabarada.tistory.com/103](https://sabarada.tistory.com/103)
- [https://devlog-wjdrbs96.tistory.com/374](https://devlog-wjdrbs96.tistory.com/374)
- [https://junghyungil.tistory.com/162](https://junghyungil.tistory.com/162)
- [우아한 레디스](https://www.youtube.com/watch?v=mPB2CZiAkKM)

## 예상 면접 질문 및 답변

### Redis란?

Redis는 고성능 키-값 저장소로서 String, list, hash, set, sorted set 등의 자료 구조를 지원하는 NoSQL이다.

### Redis는 언제 사용하는가

DB, Cache, Message Queue, Shared Memory 용도로 사용될 수 있다. 주로 Cache 서버를 구현할 때 많이 쓴다.

### Redis의 특징은?

- 영속성을 지원하는 인 메모리 데이터 저장소
- 다양한 자료 구조를 지원함.
- 싱글 스레드 방식으로 인해 연산을 원자적으로 수행이 가능함.
- 읽기 성능 증대를 위한 서버 측 리플리케이션을 지원
- 쓰기 성능 증대를 위한 클라이언트 측 샤딩 지원
- 다양한 서비스에서 사용되며 검증된 기술

### Redis는 왜 Thread Safe한가?

싱글 스레드 방식이어서 연산을 하나씩 처리한다.

### Redis는 왜 속도가 빠른가?

Key-Value 방식이므로 쿼리를 날리지 않고 결과를 얻을 수 있다.

### Redis는 영속성을 어떻게 보장하는가?

RDB 또는 AOF 방식을 통해 영속성을 보장한다.

- RDB(Snapshotting) 방식
    - 순간적으로 메모리에 있는 내용 전체를 디스크에 옮겨 담는 방식
- AOF(Append On File) 방식
    - Redis의 모든 write/update 연산 자체를 모두 log 파일에 기록하는 형태

### Redis와 Memcached의 차이는 무엇인가?

Redis와 Memcached 모두 Key-Value 방식의 저장소이지만, Redis는 다양한 자료 구조를 제공한다.

Redis는 싱글 스레드 방식이지만, Memcached는 멀티 스레드 방식이다.

Redis는 특정 시점에 데이터를 디스크에 보관하여 영속성을 보장한다.

Redis는 Pub / Sub 메시징 용도로 사용할 수 있다.
