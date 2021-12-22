**잠깐 복습!**

> NoSQL은 Not Only SQL의 약자로, RDBMS의 한계를 극복하기 위해 고안된 데이터베이스이다
> 

> 스키마가 없거나 느슨한 스키마를 제공하는 것이 가장 대표적인 특징이며, 이를 통해 분산 환경에서 대량의 데이터를 처리하는 데에 최적화되어 있는 데이터베이스이다
> 

---

- NoSQL의 특징
- CAP 이론
- NoSQL 모델 분류

# What identifies NoSQL

- **Not SQL**  
MS-SQL, Oracle, MySQL과 같은 RDBMS은 공통적인 **데이터 저장 방식**(테이블)과 **접근 방식**(SQL)을 정의하고 있지만, NoSQL은 공통의 정의가 없고 제품마다 개별적인 모델과 특성을 가지고 있다
- **Not Relational**  
NoSQL은 데이터 간의 관계를 정의하지 않는다
- **No Schema**  
NoSQL은 유동적인 스키마를 갖는다
- **분산형 구조**  
NoSQL은 고성능 머신을 사용하는 것이 아니라 일반 머신 여러대에 데이터를 분산적으로 저장/관리한다

# **CAP 이론**

**분산형 컴퓨팅은 Consistency, Availability, Partition tolerance, 3가지 특성을 만족할 수는 없다는 이론**

이에 따라 분산형 구조를 사용하는 DBMS는 3가지 중 최대 2가지 특성만 가지게 된다

- **C (Consistency, 일관성)**
    - 모든 노드가 같은 시간에 같은 데이터
    - 다중 클라이언트에서 **같은 시간에 조회하는 데이터는 항상 동일한 데이터임을 보증**하는 것
- **A (Availability, 가용성, 내고장성)**
    - 노드가 다운 되어도 다른 노드에 문제 x
    - 다중 클라이언트에서 **모든 클라이언트의 읽기와 쓰기 요청에 대하여 항상 응답이 가능해야 함을 보증**하는 것
- **P (Partition tolerance, 생존성, 분할 내성)**
    - 일부 메세지 손실해도 시스템 정상 동작
    - 지역적으로 분할된 네트워크 환경에서 동작하는 시스템에서 **두 지역 간의 네트워크가 단절되거나 네트워크 데이터의 유실이 일어나더라도 각 지역 내의 시스템은 정상적으로 동작해야 함**을 의미

CAP 이론에 따라 데이터를 관리할 때, **어떠한 특성에 중심을 두고 관리하느냐에 따라 선택하는 데이터 모델과 DBMS가 달라질 것**이다

## CAP 이론과 DBMS

![http://blog.skby.net/blog/wp-content/uploads/2018/11/1-98.png](http://blog.skby.net/blog/wp-content/uploads/2018/11/1-98.png)

### CA

Consistency + Availability, 일관성과 가용성을 만족하는 DBMS

- 시스템 다운에도 메세지 손실을 방지
- 트랜잭션 사용

→ 대표적으로 RDBMS가 있다

### CP

Consistency + Partition tolerance, 일관성과 분할 내성을 만족하는 DBMS

- 데이터보다는 성능이 중요한 퍼포먼스형에 적합

→ 대표적으로 MongoDB, Hbase가 있다

### AP

Availability + Partition tolerance, 가용성과 분할 내성을 만족하는 DBMS

- 비동기화된 서비스 스토어에 적합 (일관성을 요하지 않는 서비스)

→ 대표적으로 Cassandra가 있다

## CAP 와 NoSQL

NoSQL은 **C + P** 또는 **A + P**의 특성을 선택하여 가져가는 DBMS이다

### NoSQL의 Consistency

NoSQL 을 사용한다면 데이터의 일관성이 느슨하게 처리되어 동일한 데이터가 나타나지 않을 수 있다

느슨하게 처리된다는 것은 데이터의 변경을 시간의 흐름에 따라 여러 노드에 전파하는 것을 말한다

이러한 방법을 최종적으로 일관성이 유지된다고 하여 최종 일관성 또는 궁극적 일관성을 지원한다고 한다

다시 말해, **ACID 대신 Eventual Consistency를 허용**한다

- ACID : SQL의 모든 transaction이 만족해야 하는 특징
- Eventual Consistency : 실제 최신은 아니더라도 업데이트 전까지는 최신 데이터를 반환하겠다는 특징

### NoSQL의 **Availability**

내고장성을 가진 NoSQL 은 클러스터 내에서 몇 개의 노드가 망가지더라도 정상적인 서비스가 가능하다

몇몇 NoSQL 은 가용성을 보장하기 위해 **데이터 복제(Replication)**을 사용

→ 동일한 데이터를 다중 노드에 중복 저장하여 그 중 몇 대의 노드가 고장나도 데이터가 유실되지 않도록 하는 방법

- Master-Slave 복제 방법 : 동일한 데이터를 가진 저장소를 하나 더 생성
- Peer-to-Peer 복제 방법 : 데이터 단위로 중복 저장

# **NoSQL 데이터 모델 분류**

NoSQL의 또 다른 특징이자 장점은 다양한 데이터 모델을 제공한다는 것이다

이 때문에 도메인의 요구 사항에 걸맞는 데이터 모델을 고르고 DBMS를 설계할 수 있다

즉, NoSQL은 데이터 자체보다는 그 **데이터로 무엇을 하고 싶은가**에 초점을 맞춘다

![https://shoark7.github.io/assets/img/knowledge/nosql-data-model.jpg](https://shoark7.github.io/assets/img/knowledge/nosql-data-model.jpg)

NoSQL이 제공하는 데이터 모델은 크게 4 종류로 분류할 수 있다

- Key-Value
- Document
- Column
- Graph

## **Key-Value**

![https://phoenixnap.com/kb/wp-content/uploads/2021/04/key-value-database.png](https://phoenixnap.com/kb/wp-content/uploads/2021/04/key-value-database.png)

- 가장 기본적이고 단순한 형태 (대부분 지원함)
- 키 하나로 데이터 하나를 저장하고 조회할 수 있는 단일 키-값 구조  
→ **유니크 포인터** 사용  
→ key에는 어떠한 값도 사용 가능

Key-Value model의 경우, 단순하다는 것이 가장 큰 **특징**이며, 이에 따라 아래와 같은 특징을 가진다

- 복잡한 조회 연산을 지원하지 않는다
- 고속 읽기와 쓰기에 최적화된 경우가 많다 (고성능)
- 하나의 서비스 요청에 다수의 데이터 조회 및 수정 연산이 발생하면 트랜잭션 처리가 불가능하여 데이터 정합성을 보장할 수 없다

**사용 데이터** : 사용자의 프로필 정보, 웹 서버 클러스터를 위한 세션 정보, 장바구니 정보, URL 단축 정보 저장 등

**예시** : Redis, DynamoDB(Amazon에서 사용), Oracle NoSQL Database, Apache Ignite

## **Document**

SQL, XML, JSON 등은 데이터 관계가 존재하기 때문에 함께 묶여서 이용되는 경우가 많고, 이 때문에 데이터 처리 과정이 느려진다  
NoSql의 경우 데이터 관계를 정의하지 않기 때문에 이런 문제가 발생할 이유가 없고, 때문에 document 형태로 데이터를 관리한다

Document based model에서는 모든 데이터를 하나의 테이블에 보관한다  

![https://phoenixnap.com/kb/wp-content/uploads/2021/04/document-database.png](https://phoenixnap.com/kb/wp-content/uploads/2021/04/document-database.png)

- 키-값 모델을 개념적으로 확장한 구조
- Document (문서) = 포장되고(encapsulated) 인코딩된 데이터  
→ XML, YAML, JSON(주로 쓰임)
- 하나의 키에 하나의 구조화된 문서를 저장하고 조회  
→ key : document ID 
→ value : 구조화된 데이터 타입(XML, JSON, YAML, …)

SQL에서 레코드들을 테이블로 구성하듯이, document model에서도 document 들을 특정 **기준**으로 구성하고 분류한다

- 테이블처럼 단순 집합(Collection)
- 태그
- 문서에 대한 메타데이터
- 디렉토리 계층 구조

단순한 테이블 구조뿐만 아니라 다양한 기준으로 테이블을 구성하면서, 다양한 구조의 DBMS를 구성할 수 있다

즉, **복잡한 계층 구조 표현에 유리**하다는 장점이 있다

**사용 데이터** :

- 유연한 계층 변경이 직관적으로(JSON 사용하기 때문에) 가능 → 각 문서가 고유하고 시간에 따라 진화하는 콘텐츠 관리 시스템, 사용자 프로파일 및 카탈로그
- 읽기 성능이 좋음(B-tree index를 사용하기 때문에) → 중앙 집중식 로그 저장, 타임라인 저장, 통계 정보

**예시** : MongoDB, IBM Domino, CouchDB

## **Column**

RDBMS에서는 데이터를 row 단위로 저장한다면, column model 은 column 단위로 저장한다

각 column은 column family로 묶이고, 이 family들은 또 다른 column이 될 수 있다  

![https://phoenixnap.com/kb/wp-content/uploads/2021/04/column-oriented-database.png](https://phoenixnap.com/kb/wp-content/uploads/2021/04/column-oriented-database.png)

- 하나의 키에 여러 개의 컬럼 이름과 컬럼 값의 쌍으로 이루어진 데이터를 저장하고 조회
- 모든 컬럼은 항상 타임 스탬프 값과 함께 저장  
→ Column : 데이터 저장의 기본 단위 (column name + column value + time stamp)

Column model에서는 데이터를 row 별로 연속적으로 저장한다

![https://shoark7.github.io/assets/img/knowledge/nosql-column-based.png](https://shoark7.github.io/assets/img/knowledge/nosql-column-based.png)

이에 따라 

- 데이터 수정 시 전체 row가 아니라 특정 row 만 로드하면 되기 때문에 **불필요한 I/O 작업이 줄어든다**
- 데이터는 하나의 연속적인 entry로 취급되며, 따라서 조회 시 다른 row로 이동하는 등의 불필요한 작업이 필요 없다  
즉, **조회가 굉장히 빠르다**는 장점이 있다
- 하나의 row에 삽입되는 데이터의 형태가 동일하기 때문에 DB의 블록 하나에는 한 유형의 데이터만 존재하고, 따라서 압축이 가능하여 **디스크 공간을 절약할 수 있다**

**사용 데이터** :

- 쓰기 연산에 유리 → 채팅 내용 저장, 실시간 분석을 위한 데이터 저장소 등의 서비스 구현

**예시** : BigTable, AWS Redshift, Accumulo, Cassandra(Facebook에서 사용)

## Graph

Graph model은 데이터 뿐만 아니라 데이터 간 관계도 데이터 취급한다

![https://shoark7.github.io/assets/img/knowledge/nosql-graph.png](https://shoark7.github.io/assets/img/knowledge/nosql-graph.png)

- 데이터들의 관계를 중요시해서 저장된 데이터들이 에지로 직접 연결될 수 있어 데이터 질의 시 특정 노드와 관련된 데이터를 한 번의 OP로 획득(Retrieve) 가능
- 고도로 연결된 데이터세트를 사용하는 애플리케이션을 쉽게 구축하고 실행하는 데에 유리

**사용 데이터** : 소셜 네트워킹, 추천 엔진, 사기 감지 및 지식 그래프

**예시** : Neo4j, Giraph, IBM DB2

# 비고

## 결론

### NoSQL은 무엇인가

NoSQl은 관계형 데이터베이스의 한계를 보완하기 위해 고안된 데이터베이스 모델로, 느슨한 스키마를 제공하여 분산 컴퓨팅에서의 유연성, 확장성, 고성능성, 고기능성을 보장합니다.

### CAP 이론 설명

CAP는 Consistency, Availabilty, Partition tolerance, 3가지 특성의 약자이며, CAP 이론은 분산 컴퓨팅 환경에서 데이터 모델은 3가지 특성을 전부 가질 수는 없다는 이론입니다.

이 이론에 따라 데이터베이스들은 2가지 특성을 가지며, 데이터를 어떠한 관점으로 볼 것인가에 따라 어떤 특성을 가져갈 것인지 선택하게 됩니다

- consistency 일관성 → 클라이언트들이 같은 시간에 조화하는 데이터는 항상 동일해야 한다
- availability 가용성 → 모든 클라이언트들의 읽기/쓰기 요청에 대해 항상 응답이 가능해야 한다
- partition tolerance 분할 내성 → 분할 네트워크 환경에서 네트워크가 끊기거나 데이터가 유실되어도 각 환경 내에서는 정상적으로 동작되어야 한다

관계형 데이터베이스의 경우 consistency와 availability 특성을 가진 한편, NoSQL의 경우 partition tolerance 특성을 포함하여 2가지 특성을 가집니다.

### NoSQL 데이터베이스 모델 분류

NoSQL은 다양한 모델을 기반으로 데이터베이스를 설계할 수 있는데, 크게 4가지로 분류해 볼 수 있습니다.

- Key-Value
- Document
- Column
- Graph

## 출처

- [http://blog.skby.net/nosql-cap이론/](http://blog.skby.net/nosql-cap%EC%9D%B4%EB%A1%A0/)
- [https://phoenixnap.com/kb/nosql-data-modeling](https://phoenixnap.com/kb/nosql-data-modeling)
- [https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=minjung2623&logNo=220806878802](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=minjung2623&logNo=220806878802)
- [https://shoark7.github.io/programming/knowledge/what-is-NoSQL](https://shoark7.github.io/programming/knowledge/what-is-NoSQL)
- [https://aws.amazon.com/ko/nosql/](https://aws.amazon.com/ko/nosql/)
