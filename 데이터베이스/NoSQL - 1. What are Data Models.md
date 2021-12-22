NoSQL(Not Only SQL)은 관계형 데이터 모델(Relational Data Model)의 한계를 보완하기 위해 느슨한 스키마를 도입하여 고안된 모델이라고 많이 설명하며, 관계형 데이터 모델과 더불어 가장 많이 사용되는 데이터 모델이다

따라서 우리는 데이터 모델(Data model)이 무엇인지, 그리고 그 중 하나인 관계형 데이터 모델(Relational Data Model)과 NoSQL은 각각 무엇이며 그 특징이 무엇인지 알아볼 것이다

⇒ 데이터 모델은 무엇일까

⇒ 스키마는 무엇일까

⇒ 관계형 데이터 모델은 무엇일까

⇒ NoSQL은 무엇일까

# Data Model

데이터 모델은 **데이터의 요소**들을 정리하고 각 요소들이 서로 그리고 실제 세상의 요소들과 어떠한 **관계**를 갖고 있는지 표준화하는 **추상적인 모델**이다

> *이 데이터에는 데이터 요소 A, B, C가 있고, 요소 B와 C는 A에 포함되어 있다. A는 자동차를 나타내고 B와 C는 각각 그 자동차의 색과 크기를 표현한다*
> 

⇒ **데이터, 데이터의 관계, 데이터의 의미와 일관성, 제약조건**을 기술

![https://dataonair.or.kr/publishing/img/knowledge/SQL_001.jpg](https://dataonair.or.kr/publishing/img/knowledge/SQL_001.jpg)

우리는 데이터 모델을 통해 데이터를 실제로 이용할 수 있는 형태로 가지고 있을 수 있게 되는 것이다

## 데이터 모델이 기술하는 요소

### 기본 구성 요소

- **Entity(개체)** : DB에 표현하려는 것  
사람이 생각하는 개념이나 정보 단위 같은 **현실 세계의 대상체**이며, 무형의 정보로서 서로 연관된 몇 개의 **속성으로 구성**된다
- **Attribute(속성)** : 데이터의 가장 작은 논리적 단위  
개체를 구성하는 항목이다

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F99CCD2345B69908607](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F99CCD2345B69908607)

- **Relation(관계)** : 개체 또는 속성 간의 관계

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F9909D2365B69907A04](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F9909D2365B69907A04)

### 표시해야하는 3요소

- **Data Structure(구조)**  
논리적으로 표현된 개체 타입들 간의 관계로서 데이터 구조 및 정적 성질을 표현
- **Operation(연산)**  
DB에 저장된 실제 데이터를 처리하는 작업에 대한 명세서
- **Constraint(제약조건)**  
DB에 저장될 수 있는 실제 데이터의 논리적인 제약 조건

**예시**

관계형 데이터 모델의 3가지 요소

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fk9Tv1%2FbtqDyNhxb8R%2FP46laXG6qYBswD25ybotnk%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fk9Tv1%2FbtqDyNhxb8R%2FP46laXG6qYBswD25ybotnk%2Fimg.png)

## 데이터 모델 분류 - Three Level Architecture

ANSI/SPARC에 의하면, **데이터 모델 인스턴스**(Data Model Instance)은 크게 3가지 계층으로 분류할 수 있다

### 개념점 데이터 모델 Conceptual Data Model

가장 단순하고 추상적인 모델

- **속성**들로 기술된 개체 타입과 그들 간의 관계를 기술하여 현실 세계를 표현한다
- 이를 위해 데이터 **요구사항을 분석**하고, 어떤 자료가 필요하고 어떻게 관뢰되어야 하는지 분석해야 한다
- 핵심 **entity**와 그들 간의 **관계**를 발견하고, Entity-Relationship-Diagram(ERD)로 기술한다

### 논리적 데이터 모델 Logical Data Model

Conceptual Model을 기반으로 기본적을 프레임워크를 설정한 모델

- 개념적 모델링 과정에서 얻은 개념적 구조를 컴퓨터가 이해하고 처리할 수 있게 변환한 것이다
- 보통 데이터 모델이라 하면 논리적 데이터 모델을 가르키는 것이며, 하나의 DBMS는 하나의 논리적 모델을 선택하여 사용한다
- 데이터 간의 관계를 표현하는 방식에 따라 모델을 분류할 수 있다
    - 관계 모델(Relational Model)
    - 계층 모델(Hierarchical Model)
    - 네트워크 모델(Network Model)
    - ....
- 논리적 데이터 모델을 설계하는 과정에서 모델의 **일관성을 확보**하고 **중복을 제거**하여 속성들이 가장 적절한 엔터티에 배치되도록 하는 **정규화**가 이루어져야 한다

⇒ 가장 핵심적인 모델링 과정!

### 물리적 데이터 모델 Physical Data Model

가장 구체적인 모델

- Logical Model을 기반으로 데이터 저장소로서 어떻게 컴퓨터 하드웨어에 표현할 것인가를 표현한다
- 테이블, 컬럼으로 표현되는 **물리적인 저장구조**와 사용될 **저장 장치**, 자료를 추출하기 위해 사용될 **접근 방법** 등을 기술한다

## Schema (스키마)

DB의 **구조(개체, 속성, 관계)**와 **제약 조건**에 관한 전반적인 명세를 기술한 **메타데이터의 집합**

⇒ DB를 어떻게 구성할 것인가

ANSI/SPARC의 3단계 구성의 모델 분류에 따라 외부 단계, 개념적 단계, 내부적 단계로 서로 간섭하지 않는 모델로 분류된다

![https://dataonair.or.kr/publishing/img/knowledge/SQL_008.jpg](https://dataonair.or.kr/publishing/img/knowledge/SQL_008.jpg)

### External Schema (외부 스키마) = 사용자 뷰

사용자나 응용 프로그래머가 각 개인의 입장에서 필요로 하는 데이터베이스의 논리적 구조를 정의한 것

- 하나의 DB는 여러개의 외부 스키마가 존재할 수 있으며, 하나의 외부 스키마는 여러개의 응용 프로그램이나 사용자가 공유할 수 있다
- 전체 DB의 한 논리적인 부분이므로 **Sub Schema**라고도 부른다

### Conceptual Schema (개념 스키마) = 전체적인 뷰

DB의 전체적인 논리적 구조로서 모든 응용 프로그램이나 사용자들이 필요로 하는 데이터를 종합한 것

- 조직 전체의 DB이며, 따라서 DBA가 구성한다
- 하나의 DB는 하나의 개념 스키마를 갖는다
- **개체 간의 관계와 제약 조건, DB의 접근 권한, 보안 및 무결성 규칙**에 관한 명세를 정의한다

### Internal Schema (내부 스키마) = Storage Schema (저장 스키마)

물리적 저장 장치의 입장에서 본 DB의 구조

- 시스템 프로그래머나 시스템 설계자가 보는 관점의 스키마이다
- 실제로 DB에 저장된 레코드의 **물리적인 구조, 저장 데이터 항목의 표현 방법, 내부 레코드의 물리적 순서** 등을 정의한다

# Relational Data Model

이론적으로는 **함수 종속(Functional Dependency)에 의해 정규화(Normalization)된 모델**이라고 정의할 수 있다

테이블 형태의 저장 구조를 가지며 데이터 사이의 관계를 테이블의 키와 열을 통해 표현하는 저장 방식이다

## 기본 개념

![https://media.vlpt.us/images/full_accel/post/0395def6-5ee5-442b-8f17-c5717e8da7ff/image.png](https://media.vlpt.us/images/full_accel/post/0395def6-5ee5-442b-8f17-c5717e8da7ff/image.png)

- **Relation(릴레이션)**  
데이터가 저장되어 있는 2차원 테이블 구조  
머리 부분(**Head Intension, 내포**)인 **attribute**와 몸통 부분(**Body, Extension, 외포**)인 **tuple**로 구분할 수 있다  
**이름**이 존재하며, 데이터를 보관한다  
- **Tuple(튜플)**  
릴레이션의 **열**(Row)  
속성들의 집합이며, **Record**(레코드)나 **Instance**(인스턴스)라고도 부른다  
- **Attribute(속성)**  
릴레이션의 **행**(Column)  
개체를 구성하는 속성들을 나타내며, **Field**(필드)라고도 부른다  
이때, tuple을 유일하게 식별할 수 있는 attribute를 **Identifier**(식별자)라고 한다  
- **Relation Schema(스키마)**  
릴레이션의 **이름** + **내포**  
- **Relation Instance(릴레이션 인스턴스)**  
릴레이션에 입력된 tuple들의 집합  
- **Degree(차수)**  
릴레이션을 구성하는 속성의 수  
- **Cardinality(카디널리티)**  
릴레이션에 입력된 튜플의 수  

⇒ Degree가 1개 이상이고 Cardinality가 0개 이상이면 유효한 Relation이다

- **Domain(도메인)**  
하나의 속성이 가질 수 있는 값들의 범위

## 특징

### ACID 속성

관계형 모델은 ACID라고 불리는 4가지 속성을 만족하는 특징을 가지고 있다

- **Atomicity (원자성)**  
Transaction은 완벽하게 실행하거나, 전혀 실행되지 않는다
- **Consistency (일관성)**  
Transaction이 커밋되면, 데이터는 DB 스키마를 준수해야한다
- **Isolation (격리성)**  
동시에 일어나는 Transaction들은 각각 별도로 실행되어야 한다
- **Durability (내구성)**  
시스템 장애 시 마지막으로 알려진 상태로 복구할 수 있어야 한다

### 확장

하드웨어의 계산 성능을 개성시키거나 읽기 전용 워크 로드의 복제물을 추가하여 확장한다

### 데이터의 이용

데이터를 저장 및 검색하기 위해 SQL을 준수하는 쿼리를 이용하여 요청한다

## 한계

관계형 데이터베이스의 핵심은 **데이터를 중복해서 저장하지 않도록 설계하는 것**이다

- **데이터 구조를 효율적으로 설계하기 어렵다**는 단점이 생긴다  
- 일관성을 보장하는 관계형 모델의 특징에 의해 **수평적인 확장이 어렵다**  
- 스키마에 의해 미리 정의된 데이터 타입과 일치하는 구조화된 데이터만 관리할 수 있어 **비정형 데이터**에 대한 관리가 어렵다

# NoSQL

특정 데이터 모델에 대해 특정 목적에 맞추어 구축되는 DB로서, 현대적인 애플리케이션 구축을 위한 **유연한 스키마**를 갖추고 있다

NoSQL은 **대량의 분산 데이터**를 저장/조회하는 데에 특화되어 있으며, **개발의 용이성**, **기능성** 및 **확장성**을 인정받고 있다

## 특징

- 성능과 규모 확장에 최적화된 다양한 데이터 모델 제공
- ACID 속성을 완화여 수평적인 확장 보장
- 분산형 아키텍쳐를 사용하여 액세스 분할성 존재
- 객체 기반 API를 통해 데이터를 쉽게 저장 및 검색
- 장점
    - **유연성**  
    유연한 스키마를 제공하기 때문에 빠르고 반복적인 개발이 간으하며, 반정형 및 비정형 데이터를 다루기에 용이하다
    - **확장성**  
    고가의 강력한 서버를 추가하는 대신 분산형 하드웨어 클러스터를 이용해 확장하도록 설계되어 있다
    - **고서능**  
    특정 데이터 모델 및 액세스 패턴에 대해 최적화되어 관계형 데이터베이스보다 유사한 기능을 더 뛰어난 성능으로 처리할 수 있다
    - **고기능성**  
    각 데이터 모델에 맞추어 특별히 구축된 뛰어난 기능의 API와 데이터 유형을 제공한다
