# Hadoop

## What is hadoop

> The **Apache Hadoop software library** is a **framework** that allows for the **distributed processing of large data sets across clusters of computers using simple programming models**. It is designed to scale up from single servers to thousands of machines, each offering local computation and storage. Rather than rely on hardware to deliver high-availability, the library itself is designed to detect and handle failures at the application layer, so delivering a highly-available service on top of a cluster of computers, each of which may be prone to failures.
> 

Hadoop은 여러 개의 컴퓨터를 하나로 묶어 대용량 데이터를 처리하는 기술을 이용하여 대용량의 데이터를 적은 비용으로 더 빠르게 분석할 수 있는 플랫폼이다.

현재 빅데이터 처리와 분석을 위한 플랫폼 중 사실상 표준으로 자리잡고 있다.

## **구성**

일반적으로 하둡 분산처리시스템(HDFS)과 맵리듀스(MapReduce) 프레임워크로 시작되었으나, 여러 데이터 저장, 실행 엔진, 프로그래밍 및 데이터 처리 같은 하둡 생태계 전반을 포함하는 의미로 확장, 발전되고 있다.

이렇게 하둡 관련 오픈소스 솔루션들은 해마다 발전하여 하둡과 연동된 하둡 생태계를 "하둡 에코시스템"이라 부른다.

Hadoop은 저장된 데이터를 구문 분석하기 위해 함께 작동하는 다양한 요소의 집합으로, 다음 네 가지 모듈로 구성되어 있다

- **Hadoop Common:** 대부분의 사용 사례를 지원하는 기본 유틸리티
- **HDFS(Hadoop 분산 파일 시스템):** 쉽게 액세스할 수 있는 형식으로 데이터 저장
- **Hadoop MapReduce:** 대규모 집합으로 매핑한 다음 필터링하여 특정 결과를 찾는 방식으로 데이터 처리
- **Hadoop YARN:** 리소스 관리 및 일정 예약

![https://mblogthumb-phinf.pstatic.net/MjAyMDA4MjFfMTg5/MDAxNTk3OTc1MDUzNDk4.NSBoo0nLGYgVBKswHLlB1dXqDVAoTWa_XkvQlVP6CMQg.N40MKjZGV3aYIgPSeCp0bRKLjPyp1BdOLFvs0pMP4Cgg.PNG.acornedu/%ED%95%98%EB%91%A1%EC%9D%98_%EC%83%9D%ED%83%9C%EA%B3%84.png?type=w800](https://mblogthumb-phinf.pstatic.net/MjAyMDA4MjFfMTg5/MDAxNTk3OTc1MDUzNDk4.NSBoo0nLGYgVBKswHLlB1dXqDVAoTWa_XkvQlVP6CMQg.N40MKjZGV3aYIgPSeCp0bRKLjPyp1BdOLFvs0pMP4Cgg.PNG.acornedu/%ED%95%98%EB%91%A1%EC%9D%98_%EC%83%9D%ED%83%9C%EA%B3%84.png?type=w800)

### **분산 코디네이터**

- **ZooKeeper**
    
    분산 환경에서 **서버 간의 상호 조정이 필요한 다양한 서비스를 제공**하는 시스템  
    분산 동기화를 제공하고 그룹 서비스를 제공하는 중앙 집중식 서비스로 알맞은 분산처리 및 분산 환경을 구성하는 서버 설정을 통합적으로 관리한다  
    서브 프로젝트 대부분 동물 이름과 관련되어 있는데, 이를 관리하는 것이 주키퍼이다
    

### **분산 리소스 관리**

- **YARN**
    
    작업 스케줄링 및 클러스터 리소스 관리를 위한 **프레임 워크**  
    맵리듀스, 하이브, 임팔리, 스파크 등 다양한 애플리케이션들은 YARN에서 작업을 실행합니다
    
- **Mesos**
    
    클라우드 환경에 대한 **리소스 관리**  
    Mesos는 Linux 커널과 동일한 원칙을 사용하며 컴퓨터에 API(ex : Hadoop, Spark, Kafka, Elasticsearch 등)를 제공한다
    

### **데이터 저장**

- **HBase(분산 데이터 베이스)**
    
    글 Bigtable을 기반으로 개발된 **비관계형 데이터베이스**  
    Hadoop 및 HDFS 위에 Bigtable과 같은 기능을 제공한다
    
- **HDFS (분산 파일 데이터 저장)**
    
    애플리케이션 데이터에 대한 높은 처리량의 액세스를 제공하는 **분산파일 시스템**
    
- **Kudu (컬럼기반 스토리지)**
    
    컬럼기반 스토리지로 하둡 에코 시스템에 새로 추가되어 급변하는 데이터에 대한 빠른 분석에 이용
    

### **데이터 수집**

- **Chukwa**
    
    분산 환경에서 생성되는 데이터를 안정적으로 HDFS에 저장하는 플랫폼  
    대규모 분산 시스템을 모니터링하기 위한 시스템으로, HDFS 및 MapReduce에 구축되어 수집된 데이터를 최대한 활용하기 위한 모니터링 및 유현한 툴킷을 포함한다
    
- **Flume**
    
    많은 양의 데이터를 수집, 집계 및 이동하기 위한 분산형 서비스
    
- **Scribe**
- **Kafka**
    
    데이터 스트리밍을 실시간으로 관리하기 위한 분산 시스템
    대용량 이벤트 처리를 위해 개발되었다
    

### **데이터 처리**

- **Pig**
    
    하둡에 저장된 데이터를 맵리듀스 프로그램을 만들지 않고 SQL과 유사한 스크립트를 이용해 데이터를 처리, 맵리듀스 API를 매우 단순화한 형태로 설계되었다
    
- **Mahout**
    
    분석 기계학습에 필요한 알고리즘을 구축하기 위한 오픈소스 프레임워크 
    
- **Spark**
    
    대규모 데이터 처리를 위한 빠른 속도로 실행시켜주는 엔진  
    병렬 애플리케이션을 쉽게 만들 수 있는 80개 이상의 고급 연산자를 제공하며, 파이썬, R 등에서 대화형으로 사용할 수 있다
    
- **Impala**
    
    임팔라는 하둡 기반 분산 엔진  
    맵리듀스를 사용하지 않고 C++로 개발한 인 메모리 엔진을 사용하여 빠른 성능을 보여준다
    

****

- **Hive**
    
    하둡 기반 데이터 솔루션  
    SQL과 유사한 HiveQL이라는 언어를 제공하여 쉽게 데이터 분석을 할 수 있게 도와준다
    
- **MapReduce**
    
    대용량 데이터를 분산처리하기 위한 프로그램  
    정렬된 데이터를 분산처리(Map) 하고, 이를 다시 합치는(Reduce) 과정을 거친다
    

# Hadoop 구조

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FJH0J5%2FbtqVtceNe9c%2FwTKZ0PCb5FswcSksfPtBq1%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FJH0J5%2FbtqVtceNe9c%2FwTKZ0PCb5FswcSksfPtBq1%2Fimg.png)

Hadoop은 크게 HDFS에 파일을 저장하는 레이어, 그리고 데이터를 처리하는 레이어로 구성된 **Multi-Layer** 구조이자 **Master-Slave** 구조이다

## Multi-Layer

- **MapReduce Layer**
    - **Job Tracker** : 사용자로부터 Job을 요청받고 Task Tracker에 작업 할당
    - **Task Tracker** : Job Tracker로부터 할당 받은 작업을 MapReduce하여 결과 반환
    - 계산 담당
- **HDFS Layer**
    - **Name Node** : 작업을 해야하는 파일을 블록으로 나누어 DataNode에 전달
    - **Data Node** : 전달받은 파일의 읽기/쓰기 등을 실제로 수행
    - 저장 담당

## Master-Slave

- **Master Node**
    - 1대의 노드로 구성됨
    - **분산 파일 시스템**(DFS, Distributed File System)에 대한 정보를 가지고 있으며 **자원 할당을** 조절
    - **Name Node** : DFS를 관리하고, 어떤 Data Block이 클러스터에 저장되어 있는지 알려줌
    - **Resource Manager** : Scheduling 및 Slave node 처리 실행
- **Slave Node**
    - 실제 데이터를 가지고 있음
    - **Data Node** : 물리적으로 저장된 실제 데이터 관리
    - **Node Manager** : 노드의 task 실행

# MapReduce

**여러 노드에 task를 분배하는 방법**으로, 큰 파일을 블록 단위로 나누는 작업(**Map**), 그리고 각각 블록의 결과 정보를 합치는 작업(**Reduce**), 2단계로 이루어진다

Map Task와 Reduce Task는 입력과 출력으로 Key-Value 구조를 사용한다

- **Map**: 처리한 데이터를 (Key, Value)의 형태로 묶는 작업
    
    → List의 형태로 반환
    
- **Reduce**: Map으로 처리한 데이터에서 중복된 key 값을 지니는 데이터를 제거하여 합치고, 원하는 데이터를 추출하는 작업

## 처리 과정

1. 클라이언트는 Job(데이터, MapReduce 프로그램, 설정 정보 등)을 Job Tracker에게 보냄
2. Job Tracker는 Task Tracker들에게 Map task와 Reduce task를 할당
3. Task Tracker는 할당받은 Map task와 Reduce task를 인스턴스화 하여 수행하며, 진행 상황을 Tracker에게 보고한다

예를 들어 텍스트 파일을 저장하기 위한 작업을 한다고 생각해보자

텍스트 파일을 블록 단위로 나눈 후 모든 블록에 대해서 각 단어들이 몇 번 나왔는지 계산할 수 있다

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbqdJru%2FbtqVyUYM4qo%2FAAXd4ZPEQawzek9nT5uGpk%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbqdJru%2FbtqVyUYM4qo%2FAAXd4ZPEQawzek9nT5uGpk%2Fimg.png)

1. **Input**: 텍스트 파일을 블록 단위로 나누어 HDFS에 저장된 것을 가져온다
2. **Splitting**: 블록 안의 텍스트 파일을 한 줄로 분할한다
3. **Mapping**: 각 줄을 공백 기준으로 분리하고, Mapping을 통해 (word, 1)의 리스트로 반환한다 (Key-value 형태의 리스트)
4. **Shuffling**: 연관성 있는 데이터끼리 모아 정렬한다
5. **Reducing**: Reducing을 통해 (word, count)로 각 블록에서 특정단어가 몇번 나왔는지 계산한다
    
    → 중복 데이터를 제거하고 원하는 데이터를 추출하는 작업
    
6. **Result**: 결과를 합산하여 HDFS에 저장한다

# HDFS

**분산 파일 시스템(Hadoop Distributed File System)**

## HDFS의 목적

구글 파일 시스템을 본 떠 오픈소스로 만들어졌는데, 장시 데이터 환경의 고질병이였던 잦은 노드 실패에 대한 소프트웨어 레벨에서의 해결을 주요 목적으로 잡고있다

- **유저 스페이스 파일 시스템**
    
    파일 시스템 코드가 커널 스페이스 외부에서 실행된다
    
- **분산 파일 시스템**
    
    클러스터의 각 노드는 완전한 파일 시스템의 일부 데이터만 저장한다  
    따라서 언제든지 노드를 추가하여 전체 파일 시스템 크기를 키울 수 있다
    
- **Fault-Tolerance**
    
    노드, 파일, 블록 등이 망가지는 컴포넌트 실패에 자동으로 회복할 수 있다
    
- **큰 파일 대상의 I/O 연산과 블록 사이즈 구현**
- **append에 최적화**
    
    데이터 처리 워크로드 분석 시, 대부분이 작업은 append로 이루어지며, random write가 거의 존재하지 않고, read는 대부분 sequential하게 이루어지기 때문에 append 성능이 최적화되어 있다  
    

## 노드 (Node)

HDFS 클러스터는 마스터-슬레이브 구조로 네임 노드와 데이터노드, 2가지의 노드를 가진다

![](https://raw.githubusercontent.com/alstjgg/cs-study/main/images/database_hadoop_client_node.png)

- **네임 노드 (Namenode)**
    - 파일 시스템의 **네임 스페이스**, 파일 시스템의 **트리**, 트리에 존재하는 모든 **파일**과 **디렉토리**를 관리한다
    - 해당 정보는 **네임 스페이스 이미지**와 **Edit 로그**라는 2가지 형태로 영속적으로 저장한다
    - 어떤 파일을 구성하는 블록들이 어느 데이터 노드에 저장되어 있는지 알고 있지만, 해당 정보는 데이터 노드로부터 재구성할 수 있기 때문에 영속적으로 저장하지 않는다
- **데이터 노드 (Datanode)**
    - 파일 시스템의 **실제 저장 노드**
    - 블록은 저장, 삭제, 송신하고 가지고 있는 블록 리스트를 주기적으로 네임 노드에 보고한다 (=**block report**)
- HDFS 클라이언트 (HDFS Client)
    - 사용자 또는 어플리케이션을 대신해 네임 노드 및 데이터 노드와 통신한다
        
        → 사용자는 네임 노드와 데이터 노드를 인지하지 않고 API를 통해 접근할 수 있다
        
    - **클라이언트 - 네임 노드** : 특정 command 실행 시 클라이언트는 가장 먼저 네임노드와 통신하며, 이때 리소스에 대한 권한 확인, 파일에 해당되는 블록 및 데이터 노드 위치 확인 등의 정보를 통신한다
    - **클라이언트 - 데이터 노드** : 데이터를 쓰는 작업은 데이터 노드와의 통신을 통해 이루어진다

## 블록 (Block)

디스크는 데이터가 읽히고 써지는 가장 작은 단위 **블록**을 가지며, 파일 시스템은 디스크 블록의 몇 배가 되는 **파일 시스템 블록**을 가진다

HDFS의 파일은 블록 크기의 덩어리로 나뉘어 독립적인 단위로 저장된다

이때 HDFS의 블록은...

1. 하나의 파일이 하나의 디스크보다 클 수 있다
2. 저장소 서브시스템 관리는 단순화 한다 (단순 데이터만 가지고 있다)
3. 실패 시 데이터 복구를 위한 replication 구현 및 운영 시 부합함 등의 장점을 가져다 준다

## 블록 풀 (Block Pool)

네임 노드가 모든 파일, 블록, 디렉토리의 정보를 저장하기에 클 클러스터에서는 네임 노드 메모리가 병목되어있다  
이 문제를 해결하기 위해 **하나의 네임 노드가 파일 시스템의 일부분만 관리하도록 블록 풀을 설정**한다

블록 풀은 하나의 **네임 스페이스에 속하는 블록들의 모음**이며, 개별 블록 풀은 독립접으로 관리되기 때문에 다른 네임 스페이스와 관련 없이 새로운 블록을 생성하고 변경할 수 있다

![](https://raw.githubusercontent.com/alstjgg/cs-study/main/images/database_hadoop_blockpool.png)

## 복제 (Replication)

데이터 복제를 통해 HDFS는 **fault-tolerance**를 보장한다

파일의 블록들을 N번 복제하여 저장하기 위해 파일 단위로 **Replication Factor**를 N으로 지정한다  
예를 들어, 하나의 파일이 2개의 블록으로 구성되어 있고 replication factor를 3으로 지정할 시, 아래와 같이 데이터가 저장된다

![](https://raw.githubusercontent.com/alstjgg/cs-study/main/images/database_haddop_replicate.png)
