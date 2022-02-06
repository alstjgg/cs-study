# Data Pipeline

많은 단체들은 인사이트 도출이나 미래 계획 수립 등을 위한 대시보드나 그래프들을 생성하고자 **데이터 분석을 한다**. 이러한 결과물들을 생성해내기 위해 데이터 근원에서 생성된 raw data는 복잡한 과정을 거쳐 수집되고 정제된다. 데이터 파이프라인은 **데이터 분석을 위한 일련의 데이터 처리 과정**이다.

데이터 파이프라인은 **①source**(데이터 근원), **②sink**(데이터 목적지), **③steps**(사이 과정)으로 이루어져 있다. Source와 sink는 같을 수도 있으며, 여러개일 수도 있다. 데이터가 특정 지점에서 다른 지점으로 이동/변경될 때, 그 사이에는 데이터 파이프라인이 있는 것이다.

구체적인 목적을 가진 애플리케이션들(microservice)의 집합이 커지면서, 그 내부에서 데이터를 처리하는 과정의 효율성과 애플리케이션이나 작업들간의 의존도를 확실하게 하는 것은 아주 중요해졌다.

# Big Data Architecture

## Big Data Architecture의 필요성

데이터 양이 많아지고 데이터 베이스 시스템이 커지고 복잡해지면서, 데이터의 수집, 처리 및 분석 과정을 관리하고 담당할 수 있는 여러가지 도구들이 발전했다.

데이터가 수집되는 방법이 다양해지고 스토리지 비용은  감소했다. 빠른 속도로 도착한 데이터를 지속적으로 수집 및 처리해야 하는 경우와 느리게 도착하지만 대용량으로 학습해야하는 기록 데이터도 많아졌다. **이때 발생하는 여러가지 문제점들을 빅데이터 아키텍처를 이용하여 해결하고자 한다.**

- 미 사용 빅데이터 원본의 일괄 처리
- 사용 중인 빅데이터 실시간 처리
- 빅데이터 대화형 탐색
- 예측 분석 및 기계 학습

즉, 복잡해지는 데이터 파이프라인을 구조화하기 위해 데이터 아키텍처가 등장한 것이다.

## Data Architecture 예시

### Batch-based

![https://hazelcast.com/wp-content/uploads/2021/12/24_DataPipleline-1.png](https://hazelcast.com/wp-content/uploads/2021/12/24_DataPipleline-1.png)

**배치 처리**를 위한 구조

### Streaming

![https://hazelcast.com/wp-content/uploads/2021/12/18_StreamingDataPipeline-800x377-1.png](https://hazelcast.com/wp-content/uploads/2021/12/18_StreamingDataPipeline-800x377-1.png)

**실시간 처리**를 위한 구조

### Lambda Architecture

![Lambda Architecture](http://blog.skby.net/blog/wp-content/uploads/2019/01/1-49.png)

Lambda Architecture

**배치 처리**와 **실시간 처리**를 합친 구조

**배치, 스피드, 서빙 레이어**로 구성되어 있으며, 실시간으로 생성되는 데이터와 기록성 데이터에 대한 처리를 동시에 할수 있다는 장점으로 인해 **빅데이터 아키텍처에서 선호**된다.

- **Batch Layer**: 저장된 데이터의 일괄 처리
- **Speed Layer**: 실시간 유입 데이터 처리
- **Serving Layer**: 사용자로부터 쿼리 요청 결과 제공

**특징**

1. 파이프 라인 내의 작업들을 재시도하거나 기능을 추가하는 등의 변경을 원활하게 하기 위해 raw data를 저장한다
2. 범용성, 확장성, 결함 허용성
3. 전송 지연 최소화, 분석 결과 일관성, 고성능, 정확성

### Kappa Architecture

람다 아키텍처의 단점은 복잡하다는 것이다. 두 가지 경로로 데이터가 들어오기 때문에 두 가지 프레임워크가 사용되며, 이로 인해 중복된 계산 논리와 두 가지 아키텍처 관리가 필요해진다.

카파 아키텍처는 이를 보완하기 위해 등장 하였다.

![Kappa Architecture](http://blog.skby.net/blog/wp-content/uploads/2019/01/2-28.png)

Kappa Architecture

람다 아키텍처와 목표는 같지만, 모든 데이터가 스트림 처리 시스템을 사용하여 단일 경로를 따라 흐른다는 차이점이 있으며, 스피드와 서빙 레이어로만 구성되어 있다.

### Lambda/Kappa Architecture 구현을 위한 도구 예시

![Hadoop Ecosystem](https://mblogthumb-phinf.pstatic.net/MjAyMDA4MjFfMTg5/MDAxNTk3OTc1MDUzNDk4.NSBoo0nLGYgVBKswHLlB1dXqDVAoTWa_XkvQlVP6CMQg.N40MKjZGV3aYIgPSeCp0bRKLjPyp1BdOLFvs0pMP4Cgg.PNG.acornedu/%ED%95%98%EB%91%A1%EC%9D%98_%EC%83%9D%ED%83%9C%EA%B3%84.png?type=w800)

Hadoop Ecosystem

**Data Ingestion**

- Kafka
- Flume

**Batch Layer**

- Map & Reduce
- Spark
- Flink

**Speed Layer**

- Storm
- Spark Streaming
- Samza
- Flink

**Serving Layer**

- Elephant DB
- HBase
- Cassandra
- Hive
- Impala
- Presto

## Big Data Architecture 구성 요소

![https://docs.microsoft.com/ko-kr/azure/architecture/data-guide/big-data/images/big-data-pipeline.png](https://docs.microsoft.com/ko-kr/azure/architecture/data-guide/big-data/images/big-data-pipeline.png)

### **Data Sources**

- 데이터 원본
- 데이터가 생성되는 곳으로, 하나 이상 존재한다
- *ex. 애플리케이션 데이터 저장소, 웹 서버 로그 파일, IoT 디바이스*

### **Data Storage**

- 일괄 처리 작업을 위한 대용량 파일을 저장하는 분산 파일 저장소
- Data Lake

### **Batch Processing**

- 일괄 처리
- 일괄 처리 작업을 위한 데이터 파일 읽기, 처리, 그리고 새 파일 작성 작업을 한다
- *ex. (Hadoop)Hive, Pig, Map/Reduce, (Spark)프로그램 작성*

![https://docs.microsoft.com/ko-kr/azure/architecture/data-guide/big-data/images/batch-pipeline.png](https://docs.microsoft.com/ko-kr/azure/architecture/data-guide/big-data/images/batch-pipeline.png)

### **Real-time Message Ingestion**

- 실시간 메세지 수집, 스트림 버퍼링
- 실시간 원본 데이터를 사용하는 경우, 간단한 메세지 데이터 저장소나 복잡하게는 버퍼링, 스케일 아웃 처리, 안정적 전달 및 메세지 큐 등을 위한 기능도 제공한다
- *ex. Kafka*

### **Stream Processing**

- 스트림 처리
- *ex. Spark Streaming*

![https://docs.microsoft.com/ko-kr/azure/architecture/data-guide/big-data/images/real-time-pipeline.png](https://docs.microsoft.com/ko-kr/azure/architecture/data-guide/big-data/images/real-time-pipeline.png)

### **Analytical Data Sore**

- 분석 데이터 저장소
- 데이터를 분석 도구를 이용할 수 있는 형태의 구조화된 형식으로 처리하기 위한 저장소이다.
- *ex. 관계형 data warehouse, HBase(NoSQL DB), Hive(대화형 DB), Spark SQL*

### **Analytics and Reporting**

- 분석 및 보고
- 빅데이터 솔류션의 목적은 분석 및 보고를 통해 데이터에 대한 정보를 제공하는 것이다
- 데이터 모델링 계층, 시각화, 데이터 분석 등을 진행
- *ex. Jupyter, Spark*

### **Orchestration**

- 데이터 수집, 변환, 이동, 처리, 로드, 시각화 등의 반복되는 데이터 처리 작업 워크폴로우를 자동화한다
- *ex. Oozie*

# 출처

- [https://docs.microsoft.com/ko-kr/azure/architecture/data-guide/big-data/](https://docs.microsoft.com/ko-kr/azure/architecture/data-guide/big-data/)
- [https://blog.voidmainvoid.net/265](https://blog.voidmainvoid.net/265)
- [https://hazelcast.com/glossary/data-pipeline/](https://hazelcast.com/glossary/data-pipeline/)
- [http://blog.skby.net/람다-카파-아키텍처/](http://blog.skby.net/%EB%9E%8C%EB%8B%A4-%EC%B9%B4%ED%8C%8C-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98/)
