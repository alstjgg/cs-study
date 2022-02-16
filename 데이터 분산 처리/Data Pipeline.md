# Data Pipeline

# What is a Data Pipeline

많은 단체들은 인사이트 도출이나 미래 계획 수립 등을 위한 대시보드나 그래프들을 생성하고자 **데이터 분석을 한다**. 이러한 결과물들을 생성해내기 위해 데이터 근원에서 생성된 raw data는 복잡한 과정을 거쳐 수집되고 정제된다. 데이터 파이프라인은 **데이터 분석을 위한 일련의 데이터 처리 과정**이다.

데이터 파이프라인은 **①source**(데이터 근원), **②sink**(데이터 목적지), **③steps**(사이 과정)으로 이루어져 있다. Source와 sink는 같을 수도 있으며, 여러개일 수도 있다. 데이터가 특정 지점에서 다른 지점으로 이동/변경될 때, 그 사이에는 데이터 파이프라인이 있는 것이다.

구체적인 목적을 가진 애플리케이션들(microservice)의 집합이 커지면서, 그 내부에서 데이터를 처리하는 과정의 효율성과 애플리케이션이나 작업들간의 의존도를 확실하게 하는 것은 아주 중요해졌다.

### vs. ETL

ETL은 **추출(Extract), 변환(Transform), 적재(Load)의 줄임말**이다. 다시 말해서 데이터 파이프라인의 한 종류이며, 하나의 시스템에서 데이터를 추출하고 DB나 DW에 적재하는 과정을 가르킨다.

→ $ETL \subset DataPipeline$

Legacy ETL pipeline은 큰 덩어리의 데이터를 특정 시간에 한 공간에 저장하는 배치 작업이며, 예를 들어 시스템 트래픽이 낮아지는 새벽에 데이터 로그를 DW에 적재하는 것이다.

# Data Pipeline Solutions

데이터 근원으로부터 데이터를 가져오고, 필요에 따라 정제한 후, DW나 Data lake 같은 목적지에 다시 로드하는 과정은 물론 수동으로 할 수도 있지만, **도구를 사용하여 자동화**할 수도 있다. 이러한 data pipeline toole들은 과정을 자동화함과 동시에 작업 간 의존성 관리, 에러 처리, 모니터링과 같은 기능들도 제공한다.

## 종류

- **Batch**
    
    일정한 주기에 따라 많은 양의 데이터를 처리해야하고 실시간으로 처리할 필요가 없을 경우 사용한다
    
    - [Informatica PowerCenter](https://www.informatica.com/products/data-integration/powercenter.html)
    - [IBM InfoSphere DataStage](https://www.ibm.com/us-en/marketplace/datastage)
    - [Talend](https://www.talend.com/)
    - [Pentaho](https://www.hitachivantara.com/en-us/products/data-management-analytics/pentaho.html)
- **Real-time**
    
    Streaming source에서 발생하는 데이터를 실시간으로 처리해야할 경우 사용한다
    
    - [Hevo Data](https://hevodata.com/?utm_source=Blog_data-pipeline-tools-list&utm_medium=l1_home&utm_campaign=blog_click)
    - [Confluent](https://www.confluent.io/get-started/?utm_medium=sem&utm_source=google&utm_campaign=ch.sem_br.brand_tp.rmkt_tgt.confluent-brand_mt.mbm_rgn.apac_lng.eng_dv.all_con.confluent-SKAG-RLSA&utm_term=%2Bconfluent&creative=&device=c&placement=&gclid=Cj0KCQiAgP6PBhDmARIsAPWMq6n11h1j6KCTxxIbTtrbzeWeTvoA5BTv92-McO24cWD1Yn757hS9h20aAoNSEALw_wcB)
    - [StreamSets](https://streamsets.com/)
- **Cloud native**
    
    AWS bucket과 같은 Cloud-based data source에서 발생하는 데이터를 처리해야할 경우 사용한다
    
    - [Hevo Data](https://hevodata.com/?utm_source=Blog_data-pipeline-tools-list&utm_medium=l1_home&utm_campaign=blog_click)
    - [Confluent](https://www.confluent.io/get-started/?utm_medium=sem&utm_source=google&utm_campaign=ch.sem_br.brand_tp.rmkt_tgt.confluent-brand_mt.mbm_rgn.apac_lng.eng_dv.all_con.confluent-SKAG-RLSA&utm_term=%2Bconfluent&creative=&device=c&placement=&gclid=Cj0KCQiAgP6PBhDmARIsAPWMq6n11h1j6KCTxxIbTtrbzeWeTvoA5BTv92-McO24cWD1Yn757hS9h20aAoNSEALw_wcB)
- **Open source**
    
    직접 데이터 파이프라인을 구축하는 경우 사용한다
    
    - [Talend](https://www.talend.com/)
    - [Apache Kafka](https://kafka.apache.org/)
    - [Apache Airflow](https://airflow.apache.org/)

# DAG과 Airflow

## DAG 이란

특정한 목적을 위해 일련의 task들을 수행해야 하는 경우 data pipeline을 구축하여 이를 이룰 수 있다.

Data pipeling 내 작업들은 특정한 순서에 따라 수행되어야 하는데, 이를 사이클이 없고 순서가 있는 그래프, 즉 **DAG(Directed Acyclic Graph, 방향성 비순환 그래프)**로 표현할 수 있다.

![Data pipeline의 DAG 예시](https://www.qubole.com/wp-content/uploads/2020/07/airflow-image-2-2.jpg)

Data pipeline의 DAG 예시

이렇게 일련의 작업들을 DAG으로 표현하면 비교적 간단한 알고리즘을 통해 작업들을 수행할 수 있다.

1. 그래프 내 모든 비완료 작업(open task)에 대해,
    1. 해당 작업에 향하는 간선에 붙어있는 upstream 작업이 완료되었는지 확인한다
    2. 모든 upstream 작업들이 완료되었다면, 실행 큐에 해당 작업을 추가한다
2. 실행 큐 내 작업들을 수행하며, 완료(completed task)로 표시한다
3. 그래프 내 비완료 작업이 있다면 1로 돌아간다

같은 data pipeline에 대해 sequential script로 수행할 경우, 독립적인 작업임에도 순차적으로 수행하게 된다. 그러나 DAG에서는 병렬 처리가 가능해진다.또한 작은 작업들로 세분화하여 수행하게 되기 때문에, 오류 발생 시 전체 스크립트를 다시 돌릴 필요가 없다는 것도 장점이다.

## Workflow Manager

이렇게 DAG로 표현된 data pipeline의 task들의 수행을 관리하는 workflow manager는 다양하게 있다.

| Name | Originated at | Workflows defined in | Written in | Scheduling | Backfilling | User interface | Installation platform | Horizontally scalable |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Airflow | Airbnb | Python | Python | Yes | Yes | Yes | Anywhere | Yes |
| Argo | Applatix | YAML | Go | Third party3 |  | Yes | Kubernetes | Yes |
| Azkaban | LinkedIn | YAML | Java | Yes | No | Yes | Anywhere |  |
| Conductor | Netflix | JSON | Java | No |  | Yes | Anywhere | Yes |
| Luigi | Spotify | Python | Python | No | Yes | Yes | Anywhere | Yes |
| Make |  | Custom DSL | C | No | No | No | Anywhere | No |
| Metaflow | Netflix | Python | Python | No |  | No | Anywhere | Yes |
| Nifi | NSA | UI | Java | Yes | No | Yes | Anywhere | Yes |
| Oozie |  | XML | Java | Yes | Yes | Yes | Hadoop | Yes |

이들은 다양한 의존도와 독립성, 순서를 가진 data pipeline을 구축하고 실행하는 것을 도와준다.

다양한 workflow manager 간 가장 큰 차이점은 어떻게 workflow를 정의하는가, 그리고 스케쥴링, 모니터링 등과 같은 부가적인 서비스가 포함되어 있는가 이다.

## Airflow

### Pipeline flexibility

Airflow에서는 **python으로 짜여진 DAG file**을 통해 DAG를 정의한다. 이 파이썬 코드는 각 task와 task간 순서와 의존성을 정의하는 파이썬 스크립트로, airflow는 이를 파싱하여 DAG 구조를 알아낸다. 이 외에도 언제 어떻게 작업을 수행해야할지 등에 대한 메타 데이터도 포함하고 있다.

파이썬으로 파일을 작성하기 때문에 외부 configuration 파일, 외부 DB, 외부 클라우드 서비스, 기타 빅데이터 기술을 접목하여 data pipeling을 구축할 수 있다는 장점이 있다.

### Scheduling

Airflow는 크게 3가지 구성요소로 이루어져 있다.

- **Airflow Scheduler**
    - DAG 파싱
    - 실행 주기 확인
    - Airflow worker에게 작업을 넘겨주어 실행 준비
- **Airflow Workers**
    - 실행 될 작업을 부여받고 실행
- **Airflow Webserver**
    - 스케쥴러가 파싱한 DAG를 시각화
    - 모니터링을 위한 인터페이스 제공

![]()

*Aieflow를 이용한 data pipeline 구축 및 실행*

이 중 airflow scheduler는 위와 같은 과정을 통해 작업 스케쥴링을 하는 중요한 역할을 한다.

### Monitoring

Airflow에서는 실행 결과 모니터링을 위한 웹 인터페이스를 제공하고 있다

![]()

각 workflow DAG를 한눈에 확인할 수 있는 graph view 뿐만 아니라 tree view를 통해 실행 이력도 한눈에 확인할 수 있다*DAG tree view*

Airflow는 실패한 작업에 대해 retry를 할 수 있는 기능을 제공하며, 그래도 오류가 발생하면 실패로 처리하고 선택에 따라 notification도 보내준다.

### Incremental loading & Backfilling

Airflow의 스케쥴러는 cron과 같이 특정한 시간에 실행하는 것 뿐만 아니라 직전 직후 스케쥴링에 대한 정보도 이용할 수 잇다는 강점이 있다. 이를 이용하여 큰 데이터 셋을 매번 처리하는 것이 아니라 이전 스케쥴에서 작업 한 데이터 이후 데이터만 작업할 수 있다.

또한, backfilling을 이용하여 과거에 작업했던 내용을 재실행할 수도 있다.

## Airflow DAG 구축하기

기본적인 data pipeline과 task 설계는 완료했다고 가정한다.

### 1. Instantiate a DAG object

DAG object를 만드는 것이 가장 첫번째 단계이다.

Workflow 내 모든 task는 이 DAG object를 바라보고 있으며, 이를 통해 airflow는 어떤 task가 어떤 DAG에 해당하는지 알 수 있게 된다.

```
dag = DAG(
   dag_id="DAG Name",  # 1
   start_date=airflow.utils.dates.days_ago(14),  # 2
   schedule_interval=None,  # 3
)
```

- 1 > 웹에 표기될 DAG 이름
- 2 > DAG이 처음 시작할 날짜
- 3 > 실행 주기 스케쥴

이 중 1, 2번은 반드시 포함해줘야 한다.

### 2. Instantiate a BashOperator

Airflow workflow scipt에는 실제 작업을 하는 한개 이상의 **Operator**를 가진다.

각 operator는 작업의 한 조각을 수행하고, 이러한 여러개의 operator가 하나의 workflow를 구성하게 된다.

### Operator vs Task

Operator와 task 모두 작업을 하기 위한 코드이다.

Operator는 실제 작업을 위한 코드가 구현되어 있으며, airflow는 *BaseOperator*라는 클래스를 상속한 여러가지 operator를 제공하고 있다. *BashOperator*나 *PythonOperator*와 같이 범용적인 작업을 위한 것과 *EmailOperator*나 *SimpleHTTPOperator*와 같이 특정 작업을 수행하기 위한 것이 있다.

Task는 이러한 operator의 실행을 돕는 일종의 wrapper와 같은 역할을 한다.

![]()

사용자는 operator의 구현에 신경을 쓰면 되고, airflow는 task가 제대로 실행되는지 신경쓴다.

### Sensors & Hooks

Operator 외에도 작업을 수행하기 위해 제공되는 sensor와 hook이 있다.

Seonsor는 특정 내/외부 트리거를 기다리기 위한 일종의 operator로, ExternalTaskSensor, HivePartitionSensor, S3KeySensor, 등이 있다.

Hook는 S3, MySQL, Hive 등과 같은 airflow 외부 시스템과 연결하기 위한 인터페이스를 제공한다.

### BashOperator

예를 들어 bash command를 실행하기 위한 BashOperator가 있을 수 있다.

```
task1 = BashOperator(
   task_id="task",  # 1
   bash_command="curl -o /tmp/launches.json 'https://ll.thespacedevs.com/2.0.0/launch/upcoming'",  # 2
   dag=dag,  # 3
)
```

- 1 > task 이름
- 2 > 실제 실행할 bash command
- 3 > 앞서 생성했던 dag object

### PythonOperator

Python script를 실행하기 위해 PythonOperator를 사용할 수 있다.

```
def _python_function():  # 1
   pass

task2 = PythonOperator(
   task_id="python_function",  # 2
   python_callable=_python_function,  # 3
   dag=dag,  # 4
)

```

- 1 > 실행할 파이썬 함수 정의
- 2 > task 이름
- 3 > 실행할 파이썬 함수 명시
- 4 > 앞서 생성했던 dag object

이렇듯 파이썬 함수는 PythonOperator instance와 실행될 함수, 2가지를 정의해야 한다.

### 3. Define order of task execution

각 operator는 독립적으로 수행되며, **dependency**를 정의하여 이들 간 실행 순서를 정의할 수 있다.

```
task1 >> task2 >> task3
```

">>"를 통해 순서를 정의한다.

# 출처

- [https://www.alooma.com/blog/what-is-a-data-pipeline](https://www.alooma.com/blog/what-is-a-data-pipeline)
- Data pipelines with Apache Airflow
- [https://hevodata.com/blog/data-pipeline-tools-list/](https://hevodata.com/blog/data-pipeline-tools-list/)
