# 1. 실행계획 (MySQL Explain)
## 정의
- ```SQL문으로 요청한 데이터를 어떻게 불러올 것인지에 관한 계획(경로)```
- MySQL 튜닝에서 가장 중요한 것은 쿼리와 스키마 최적화이다. Slow Query를 없애는 것은 성능을 향상 시키기 위한 매우 중요한 수단이므로 어플리케이션의 성능을 향상 시키고 싶다면 우선적으로 쿼리의 튜닝을 검토해봐야 한다.
- 성능 진단의 첫걸음은 실행한 SQL이 DB에서 어떻게 처리되는 지를 파악하는 것이다. 사람이 이해할 수 있는 언어로 구성된 SQL을 실행하도록 DB에 명령을 내리면 DB는 내부적으로 여러 단계를 거친 후 실제 데이터를 찾는다.
MySQL Explain 이란 **DB가 테이터를 찾아가는 일련의 과정을 사람이 알아보기 쉽게 DB 결과 셋으로 보여주는 것**이다. MySQL Explain을 활용하면
기존의 쿼리를 튜닝할 수 있을 뿐만 아니라 성능 분석, 인덱스 전략 수립 등과 같이 성능 최적화에 대한 전반적인 업무를 처리할 수 있다.

## 특징

- 실행계획을 확인하는 키워드로는 ```EXPLAIN, ESCRIBE, DESC```가 있다. 3가지 중 어떤 키워드를 사용해도 실행 계획의 결과는 같으며, 확인하고자 하는 SQL문 앞에 원하는 키워드를 작성하면 된다.
- 다른 DBMS와는 달리 MySQL에서는 쿼리의 일부분을 직접 실행할 때도 있기 때문에 쿼리 자체가 상당히 복잡하고 무거운 쿼리인 경우에는 실행 계획의 조회 또한 느려질 가능성이 있다.
- UPDATE나 INSERT, DELETE 문장에 대해서는 실행 계획을 확인할 방법이 없다.


# 2. 실행계획 (MySQL Explain) 결과의 각 항목 별 의미
- EXPLAIN 키워드를 붙여서 실행 계획 정보를 제공받을 때 테이블 형식으로 결과가 보여진다. (MySQL 8.0부터는 JSON이나 TREE로도 볼 수 있음)

|구분|설명|
|---|---|
|id|select 아이디로 SELECT를 구분하는 번호|
|table|	참조하는 테이블|
|select_type|	select에 대한 타입|
|type|조인 혹은 조회 타입|
|possible_keys|	데이터를 조회할 때 DB에서 사용할 수 있는 인덱스 리스트|
|key|	실제로 사용할 인덱스|
|key_len|	실제로 사용할 인덱스의 길이|
|ref|액세스 조건(참조 조건)에 사용된 컬럼|
|rows|쿼리 실행 시 조사하는 행 |
|extra|	추가 정보|

<br>

## 1) id 컬럼

- SELECT 쿼리를 구분하기 위한 용도의 컬럼 (SELECT문이 몇 개인지를 확인한다고 보면 된다.)
- SQL문이 실행되는 차례를 표기한 값
- SELECT 쿼리에 JOIN을 통해 여러 개의 테이블을 조회하더라도 연관된 레코드는 같은 ID가 부여된다.

```sql
EXPLAIN
SELECT e.emp_no, e.first_name, s.from_date, s.salary
FROM empmloyees e, salaries s
WHERE e.emp_no=s.emp_no
LIMIT 10;
```
![1](https://user-images.githubusercontent.com/48278519/150068681-1c6f7d27-7739-4215-9d68-dc8e21137c85.JPG)

- SELECT 쿼리 내부에서 서브 쿼리(SELECT)를 사용한다든지 UNION을 사용하여 여러 테이블을 조회하는 경우, 서브 쿼리와 UNION 쿼리로 조회되는 것은 다른 ID가 부여된다.
```sql
EXPLAIN
SELECT
( (SELECT COUNT(*) FROM employees) + (SELECT COUNT(*) FROM departments) ) AS total_count;
```
![2](https://user-images.githubusercontent.com/48278519/150068708-cac7825a-8159-4d45-86cf-26ad0a81ecee.JPG)

<br>

## 2) select_type 컬럼
- SELECT 문의 유형을 출력하는 컬럼

|구분|설명|
|---|---|
|SIMPLE|	단순 SELECT (Union 이나 Sub Query 가 없는 SELECT 문)|
|PRIMARY|	Sub Query를 사용할 경우 Sub Query의 외부에 있는 쿼리(첫번째 쿼리), UNION 을 사용할 경우 UNION의 첫 번째 SELECT 쿼리|
|UNION|	UNION 쿼리에서 Primary를 제외한 나머지 SELECT |
|DEPENDENT_UNION|	UNION을 사용한 경우 중 UNION으로 결합된 쿼리가 외부 쿼리에 의해 영향을 받는 대상 |
|UNION_RESULT|UNION 구문의 결과를 담는 임시 테이블|
|SUBQUERY|	Sub Query 또는 Sub Query를 구성하는 여러 쿼리 중 첫 번째 SELECT문|
|DEPENDENT_SUBQUERY	Sub| Sub Query를 사용한 경우 중 바깥 SELECT 쿼리에서 정의된 컬럼을 사용하는 경우|
|DERIVED|	SELECT로 추출된 테이블 (FROM 절 에서의 서브쿼리 또는 Inline View)|
|UNCACHEABLE SUBQUERY|	Sub Query와 동일하지만 Cache된 결과를 사용할 수 없음|
|UNCACHEABLE| UNION 과 동일하지만 Cache된 결과를 사용할 수 없음|

<br>

### #SIMPLE
- **UNION이나 서브쿼리를 사용하지 않은 단순 SELECT 쿼리의 경우 SIMPLE로 표시** (JOIN한 경우도 SIMPLE 타입으로 친다.)
- 실행 계획 테이블에서 select_type이 SIMPLE인 쿼리는 반드시 하나만 존재한다.
- 일반적으로 제일 바깥 SELECT 쿼리의 select_type이 SIMPLE로 표시된다.
![3](https://user-images.githubusercontent.com/48278519/150069457-eff625f4-54ea-460e-aa6a-662b8efe02b1.png)

<br>

### #PRIMARY
- **UNION, UNION ALL, 서브쿼리가 포함된 SELECT 쿼리의 경우 가장 바깥쪽(UNION에선 첫번째)에 있는 SELECT 쿼리**가 PRIMARY로 표시
- select_type이 PRIMARY인 쿼리는 반드시 하나만 존재한다.
![4](https://user-images.githubusercontent.com/48278519/150069471-6e794041-0b8f-4c5d-9121-c662aa3cfa30.png)

<br>

### #UNION 
- **UNION / UNION ALL으로 결합하는 단위 SELECT 쿼리문에서 두 번째 이후 쿼리**를 UNION으로 표시
- UNION에 사용된 첫 번째 SELECT 쿼리는 UNION의 결과를 대표하는 select_type으로 설정

```sql
EXPLIAN
SELECT * FROM (
  (SELECT emp_no FROM employees e1 LIMIT 10)
  UNION ALL
  (SELECT emp_no FROM employees e2 LIMIT 10)
  UNION ALL
  (SELECT emp_no FROM employees e3 LIMIT 10)
) tb;
```
![5](https://user-images.githubusercontent.com/48278519/150069696-7c0e44d8-c32f-493d-9c03-d265be9fd2d1.JPG)


- UNION이 되는 단위 SELECT 쿼리 3개 중에서 첫 번째(e1 테이블)만 UNION이 아니고, 나머지 2개는 모두 UNION으로 표시돼 있다. 대신 UNION의 첫 번째 쿼리는 전체 UNION의 결과를 대표하는 select_type으로 설정됐다.
- 위 예시에선 세 개의 서브 쿼리로 조회된 결과를 UNION_ALL로 결합해 임시 테이블을 만들어서 사용하고 있으므로 UNION ALL의 첫 번째 쿼리는 DERIVED라는 select_type을 갖는다.

<br>

### #DEPENDENT UNION 
- **UNION을 사용한 경우 중에서도 UNION으로 결합된 쿼리가 외부 쿼리에 의해 영향을 받는 것을 표기**
- UNION으로 연결된 단위 쿼리 중 두번째 이후 단위 쿼리에 해당 
- 내부 쿼리가 외부의 값을 참조해서 처리될 때 DEPENDENT 키워드가 추가된다.

```sql
EXPLAIN
SELECT
  e.first_name,
  ( SELECT CONCAT('Salary change count : ', COUNT(*) AS message
      FROM salaries s WHERE s.emp_no=e.emp_no
    UNION
    SELECT CONCAT('Department change count : ', COUNT(*) AS message
      FROM dept_emp de WHERE de.emp_no=emp_no
  ) AS message
FROM employees e
WHERE e.emp_no=10001;
```
![6](https://user-images.githubusercontent.com/48278519/150069709-2be37312-3b1d-4034-bb0a-b2b40184bfe8.JPG)

- 두 개의 SELECT 쿼리가 UNION으로 결합되었고, 서브 쿼리의 외부(Outer)에서 정의된 employees 테이블의 emp_no 칼럼을 사용하고 있다. 
- 이렇게 ```내부 쿼리가 외부의 값을 참조해서 처리될 때 DEPENDENT 키워드가 select_type에 표시```된다.

- **참고)** 하나의 단위 SELECT 쿼리가 다른 단위 SELECT를 포함하고 있으면 이를 ```서브 쿼리```라고 표현한다. 
  - 서브 쿼리가 사용된 경우에는 외부(Outer) 쿼리보다 서브 쿼리가 먼저 실행되는 것이 일반적이며, 대부분 이 방식이 반대의 경우보다 빠르게 처리된다. 
  - 하지만 select_type에 "DEPENDENT" 키워드를 포함하는 서브 쿼리에는 외부 쿼리에 의존적이므로 외부 쿼리보다 먼저 실행될 수가 없다.
  -  그래서 select_type에 "DEPENDENT" 키워드가 포함된 서버 쿼리는 비효율적인 경우가 많다.

<br>

### #UNION RESULT 
- **UNION ALL이 아닌 UNION 구문의 결과를 담는 임시 테이블**
- MySQL에서 UNION ALL이나 UNION (DISTINCT) 쿼리는 모두 UNION의 결과를 임시 테이블로 생성하게 되는데, 실행 계획상에서 이 **임시 테이블**을 가리키는 라인의 select_type이 UNION RESULT
- MySQL8.0부터 UNION ALL을 쓸 때는 결과를 담을 임시 테이블을 생성하지 않지만 UNION 또는 UNION DISTINCT 쿼리는 임시 테이블에 결과를 담기 때문에 실행 계획 상 UNION RESULT는 UNION의 결과를 담는 임시 테이블을 의미함
- UNION RESULT는 실제 쿼리에서 단위 쿼리가 아니기 때문에 별도로 id 값은 부여되지 않는다.

```sql
EXPLAIN
SELECT emp_no FROM salaries WHERE salary>100000
UNION ALL
SELECT emp_no FROM dept_emp WHERE from_date>'2001-01-01';
```
![7](https://user-images.githubusercontent.com/48278519/150069770-8b0cdf6e-ca0a-4207-bc41-d6e2d9f2d97f.JPG)

-  "UNION RESULT" 라인의 table 칼럼은 "<union1,2>"로 표시돼 있는데, 아래 그림과 같이 id가 1번인 단위 쿼리의 조회 결과와 id가 2번인 단위 쿼리의 조회 결과를 UNION 했다는 것을 의미한다.

![8](https://user-images.githubusercontent.com/48278519/150069773-37d1d2f0-78f8-4ed5-8472-b57162dd5ff0.png)

<br>

### #SUBQUERY 
- FROM 절에 사용된 서브 쿼리는 DERIVED 로 표기
- 그 밖의 위치에서 사용된 서브 쿼리는 전부 SUBQUERY라고 표시된다. MySQL 매뉴얼에서 사용하는 "파생 테이블"이라는 단어는 DERIVED와 같은 의미로 이해하면 된다.

```sql
EXPLAIN
SELECT
  e.first_name,
  (SELECT COUNT(*) FROM dept_emp de, dept_manager dm WHERE dept_no=de.dpet_no) AS cnt 
FROM employees e
WHERE e.emp_no=10001;
```
![8](https://user-images.githubusercontent.com/48278519/150071309-5bd278c3-1082-4249-ad17-4030903c4929.JPG)


<br>

### #DEPENDENT SUBQUERY 
- **FROM절 이외에 사용된 서브 쿼리가 바깥쪽 SELECT 쿼리에서 정의된 컬럼을 사용하는 경우**에 해당 서브 쿼리에 표기
- 서브 쿼리 / UNION 중 첫번째 단위 쿼리가 바깥쪽(Outer) SELECT 쿼리에서 정의된 칼럼을 사용하는 경우
- DEPENDENT UNION과 같이 DEPENDENT SUBQUERY 또한 외부 쿼리가 먼저 수행된 후 내부 쿼리(서브 쿼리)가 실행돼야 하므로 튜닝의 대상
- 예시 1
```sqlEXPLAIN
SELECT e.first_name,
  (SELECT COUNT(*) 
   FROM dept_emp de, dept_manager dm
   WHERE dm.dept_n=de.dept_no AND emp_no=e.emp_no) AS cnt
 FROM employees e
 WHERE e.emp_no=10001;
```

![9](https://user-images.githubusercontent.com/48278519/150071621-6d46c406-8cc6-4aca-925b-5a2aceb8cb9e.JPG)

- 예시2

![10](https://user-images.githubusercontent.com/48278519/150071644-4c3b2648-846d-4f98-b634-2647124763a6.png)


<br>

### #DERIVED 
- **FROM절에 사용된 서브 쿼리로 SELECT 쿼리의 결과로 메모리나 디스크에 임시 테이블을 만드는 경우** 표기
- select_type이 DERIVED인 경우에 생성되는 임시 테이블을 파생 테이블이라고도 한다.
- MySQL은 FROM 절에 사용된 서브 쿼리를 제대로 최적화하지 못할 때가 대부분이다. 파생 테이블에는 인덱스가 전혀 없으므로 다른 테이블과 조인할 때 성능상 불리할 때가 많다.
- 따라서 가능하다면 DERIVED 형태의 실행 계획을 조인으로 해결할 수 있게 바꿔주는 것이 좋다.
```sql
EXPLAIN
SELECT *
FROM
  (SELECT de.emp_no FROM dept_emp de) tb,
  employees e
WHERE e.emp_no=tb.emp_no;
```
![10](https://user-images.githubusercontent.com/48278519/150071854-e43f08b8-2c1b-4d2a-b5eb-2984e753881e.JPG)


<br>

### #UNCACHEABLE SUBQUERY 
- 메모리에 상주하여 **재활용되어야 할 서브쿼리가 재사용되지 못할 때** 표기
- SUBQUERY, DEPENDENT SUBQUERY는 서브 쿼리 결과를 캐시할 수 있는데 특정 조건때문에 캐시를 이용할 수 없을 때 표기
( SUBQUERY는 바깥쪽(Outer)의 영향을 받지 않으므로 처음 한 번만 실행해서 그 결과를 캐시하고 필요할 때 캐시된 결과를 이용하고, DEPENDENT SUBQUERY는 의존하는 바깥쪽(Outer) 쿼리의 칼럼의 값 단위로 캐시해두고 사용)
<br>
- 하지만 다음의 3가지 경우에선 캐시를 재사용하지 못하는 경우 발생 
  - ```사용자 변수가 서브 쿼리에 들어갔거나```
  -  ```UUID(), RAND() 같이 결과값이 호출할 때마다 변경되는 함수가 서브 쿼리에 들어갔거나```
  - ```NOT-DETERMINISTIC 속성의 stored function이 서브 쿼리에 들어간 경우```

```sql
EXPLAIN
SELECT *
FROM employees e
WHERE e.emp_no (
	SELECT @status FROM dept_emp de WHERE de.dept_no='d005'
);
```
![11](https://user-images.githubusercontent.com/48278519/150072099-e25a8900-1517-49d2-bd99-842cad9768bf.JPG)


### #MATERIALIZED
- **FROM절이나 IN 절 구문에 연결된 서브쿼리가 임시테이블을 생성한 뒤, 조인이나 가공 작업 등을 통해 최적화 할 때** 출
- 즉, IN절의 서브쿼리를 임시 테이블로 만들어서 조인 작업을 수행하는 것
- 보통 서브 쿼리보다 외부 쿼리의 테이블을 먼저 읽어서 비효율적으로 실행되기 마련인데 이렇게 실행하지 않고 서브 쿼리의 내용을 임시테이블로 구체화한 후 외부 테이블과 조인하는 형태로 최적화된다.
![11](https://user-images.githubusercontent.com/48278519/150072526-edb37ac3-da81-4e79-b32b-725ca0cd74d7.png)


## Select_type 칼럼의 주의 대상

### #DERIVED
DERIVED는 FROM 절에 사용된 서브 쿼리로부터 발생한 임시 테이블을 의미한다.<br>
임시 테이블은 메모리에 저장될 수도 있고 디스크에 저장될 수도 있는데 일반적으로 메모리에 저장하는 경우에는 크게 성능에 영향을 미치지 않지만, ```데이터의 크기가 커서 임시 테이블을 디스크에 저장하면 성능이 떨어진다.```

<br>

### #UNCACHEABLE SUBQUERY
쿼리의 FROM 절 이외의 부분에서 사용하는 서브 쿼리를 의미한다.<br>
가능한 MySQL 옵티마이저가 최대한 캐시되어 재사용될 수 있게 유도하지만 사용자 변수나 일부 함수가 사용된 경우에는 이러한 캐시 기능을 사용할 수 없다.
따라서 이런 실행 계획이 사용된다면 ```사용자 변수를 제거하거나 다른 함수로 대체해서 사용 가능할지 검토```해 보는 것이 좋다.


<br>

### #DEPENDENT SUBQUERY
쿼리의 FROM 절 이외의 부분에서 사용하는 서브 쿼리가 자체적으로 실행되지 못하고, 외부 쿼리에서 값을 전달받아 실행되는 경우를 의미한다.<br>
이는 서브 쿼리가 외부 쿼리의 결과 값에 의존적이기 때문에 전체 쿼리의 성능을 느리게 만들기 때문에 ```가능하다면 외부 쿼리와의 의존도를 제거하는 것```이 좋다.


<br>

## 3) table 컬럼
- EXPLAIN 키워드로 실행 계획을 확인할 때, SELECT 쿼리로 분류하는게 아니라 테이블 단위로 분류해서 결과가 나오기 때문에 **각 행이 어떤 테이블에 접근하는 지를 보여주는 것**으로 대부분의 경우 테이블 이름이나 SQL에서 지정된 별칭을 나타냄
- 별도의 테이블을 사용하지 않는 SELECT 쿼리의 경우에는 table 칼럼에 NULL이 표시된다.
- table 컬럼에 <> 로 감싸져있는 테이블은 임시 테이블을 의미한다. <>안에 표시되는 숫자는 단위 SELECT 쿼리의 id를 지칭한다. 

**(예시)**

![12](https://user-images.githubusercontent.com/48278519/150073886-8510dcc3-25f2-48d7-821d-01ee86a03a5d.JPG)

위 예시를 보면, id = 1의 table 칼럼의 값이 <derived2>인데, 이것은 id = 2번인 실행 계획으로부터 만들어진 파생 테이블을 가리킨다. <br>
또한, 단위 SELECT 쿼리의 id 2번은 dept_emp 테이블로부터 SELECT된 결과가 저장된 파생 테이블이라는 점을 알 수 있다.
  
![13](https://user-images.githubusercontent.com/48278519/150074097-ae1e5dbc-9c9d-470d-85c6-bdad0a503897.png)

<br>
- 위 예시의  실행 계획을 분석해 보자.
  
```sql
1. 첫 번째 테이블이 <derived2>라는 것으로 보아 해당 라인보다 쿼리의 id가 2번인 라인이 먼저 실행되고, 그 결과가 파생 테이블로 준비돼야 한다는 것을 알 수 있다.
  
2. 세 번째 라인의  id = 2번을 보면, select_type 칼럼의 값이 DERIVED로 표시돼 있다.
  즉, 이 라인은 table 칼럼에 표시된 dept_emp 테이블을 읽어서 파생 테이블을 생성하는 것을 알 수 있다.
  
3. 세 번째 라인의 분석이 끝났으므로 다시 실행 계획의 첫 번째 라인으로 돌아가자.
  
4. 첫 번째 라인과 두 번째 라인은 같은 id 값을 가지고 있는 것으로 봐서 2개 테이블(첫 번째 라인의 <derived2>와 두 번째 라인의 e 테이블)이 조인되는 쿼리라는 사실을 알 수 있다. 
그런데 <derived2> 테이블이 e 테이블보다 먼저(윗 라인)에 표시됐기 때문에 <derived2>가 드라이빙 테이블이 되고, e 테이블이 드리븐 테이블이 된다는 것을 알 수 있다.
즉, <derived2> 테이블을 먼저 읽어서 e 테이블로 조인을 실행했다는 것을 알 수 있다.
```

- 위 예시의 실제 쿼리는 다음과 같다.
  
```sql
SELECT *
FROM
  (SELECT de.emp_no FROM dept_emp de) tb,
  employees e
WHERE e.emp=tb.emp_no;
```
  
  
## 4) partitions 컬럼
- 실행 계획의 부가 정보로, 파티셔닝(partitioning)하여 테이블을 관리한다고 가정할 때, **어떤 파티션을 읽었는지를 알려주는 정보**
- 5.7 이전에는 EXPLAIN PARTITION 명령을 해야 보였으나 8.0 부터는 EXPLAIN 명령만해도 보이게 되었다.
- 사전에 정의한 전체 파티션 중 특정 파티션에 선택적으로 접근하는 것이 SQL성능 측면에서 유리 
  
  ![14](https://user-images.githubusercontent.com/48278519/150075228-4de63ed2-5d52-43cc-9912-8e43feecdb29.png)

<br>
  
## 5) type 컬럼
  
- MySQL 서버가 **각 테이블의 레코드를 어떤 방식으로 읽었는지 정보를 나타내는 컬럼**
- 실행 계획이 적절하게 인덱스를 참조했는지를 확인하는데 가장 핵심인 컬럼
- 하나의 단위 SELECT 쿼리는 아래 접근 방법 중에서 단 하나만 사용할 수 있다. 또한 index_merge를 제외한 나머지 접근 방법은 반드시 하나의 인덱스만 사용한다. 그러므로 실행 계획의 각 라인에 접근 방법이 2개 이상 표시되지 않으며, index_merge 이외의 type에서는 인덱스 항목에도 단 하나의 인덱스 이름만 표시된다.
**참고)** MySQL의 매뉴얼에서는 type 칼럼을 조인 타입으로 소개하고 하나의 테이블로부터 레코드를 읽는 작업도 조인처럼 처리한다. 그래서 SELECT 쿼리의 테이블 개수와 관계없이 실행 계획의 type 칼럼을 조인 타입이라고 명시하고 있다. 하지만 크게 조인과 연관 지어 생각하지 말고, 각 테이블의 접근 방식(Access type)으로 해석하면 된다.


|구분|설명|
|---|---|
|system|테이블에 단 한개의 데이터만 있는 경우|
|const|	SELECT에서 Primary Key 혹은 Unique Key를  조회하는 경우로 한 건의 데이터만 반환|
|eq_ref|조인 할 때 첫 번째 읽은 테이블의 칼럼값을 이용해 두 번째 이후 읽는 테이블의 프라이머리 키/유니크 인덱스 컬럼(NOT NULL)의 동등 조건에 사용하는 경우|
|ref| 조인 할 때 JOIN 순서에 상관없이 사용되며, 인덱스의 종류와 상관없이 동등(equal)조건이 사용될 때 사용하는 경우|
|ref_or_null|	ref 와 같지만 null 이 추가되어 검색되는 경우|
|index_merge|	두 개의 인덱스가 병합되어 검색이 이루어지는 경우|
|unique_subquery|	IN 절 안의 서브쿼리에서 Primary Key가 오는 특수한 경우|
|index_subquery|unique_subquery와 비슷하나 Primary Key가 아닌 인덱스인 경우|
|range|	특정 범위 내에서 인덱스를 사용하여 원하는 데이터를 추출하는 경우|
|index|	인덱스를 처음부터 끝까지 찾아서 검색하는 경우로, 일반적으로 인덱스 풀스캔이라고 함|
|all|	테이블을 처음부터 끝까지 검색하는 경우로, 일반적으로 테이블 풀스캔이라고 함|

<br>

### #system
- 레코드가 0건 또는 1건만 존재하는 테이블을 접근할 때의 방법 
- InnoDB 테이블에서는 나타나지 않고, MyISAM이나 MEMORY 테이블에서만 사용되는 접근 방식
- 성능상 최상의 type

<br>
  
### #const 
- 쿼리에 프라이머리 키/유니크 키 컬럼을 이용하는 where 조건에 있으면서 결과가 반드시 1건을 반환하는 쿼리로 접근할 때의 방법 (= UNIQUE INDEX SCAN)
- ```결과가 1개인 것을 쿼리 전에 DBMS가 예측할 수 있어야 한다.``` (쿼리 실행 후에 결과가 1개인 것은 의미없음)
- 조인의 순서와 관계없음
  
```sql
EXPLAIN
SELECT * FROM employees WHERE emp_no=10001;
```
![14](https://user-images.githubusercontent.com/48278519/150076919-280599bb-16f3-44fb-b82e-f03e039e86be.JPG)

  
### #eq_ref 
- **JOIN에서  첫 번째 읽은 테이블의 칼럼값을 이용해 두 번째 이후 읽는 테이블의 프라이머리 키/유니크 인덱스 컬럼(NOT NULL)의 동등 조건에 사용될 때**
- 여러 테이블이 JOIN되는 쿼리에서만 발생
- 두 번째 테이블은 반드시 1건의 레코드만 반환할 때 사용


### #ref
- 'eq_ref'와 달리 JOIN 순서에 상관없이 사용되며, 인덱스의 종류와 상관없이 동등(equal)조건이 사용될 때 접근 방법 
- 프라이머리 키나 유니크 인덱스 등의 제약 상관없이 사용된다.
- 드리븐 테이블의 데이터 접근 범위가 2개 이상일 경우 사용된다.
  
```sql
EXPLAIN
SELECT * FROM dept_emp WHERE dept_no='d005';
```
![15](https://user-images.githubusercontent.com/48278519/150076963-f517bad7-d7a3-485e-ba99-b68eadc178f8.JPG)

  
dept_emp 테이블의 프라이머리 키를 구성하는 칼럼(dept_no + emp_no) 중 일부만 동등(Equal) 조건으로 WHERE 절에 명시됐기 때문에 조건에 일치하는 레코드가 1건이라는 보장이 없다. 그래서 const가 아닌 ref 접근 방법이 사용되었다.

|type|설명|
|---|---|
|const|조인의 순서와 관계없이 프라이머리 키나 유니크 키의 모든 칼럼에 대해 동등(Equal) 조건으로 검색(반드시 1건의 레코드만 반환)|
|eq_ref|조인에서 첫 번째 읽은 테이블의 칼럼값을 이용해 두 번째 테이블을 프라이머리 키나 유니크 키로 동등(Equal) 조건 검색(두 번째 테이블은 반드시 1건의 레코드만 반환)|
|ref|조인의 순서와 인덱스의 종류에 관계없이 동등(Equal) 조건으로 검색(1건의 레코드만 반환된다는 보장이 없어도 됨)|
  
<br>
  
### #fulltext 
- MySQL의 전문 검색(Fulltext) 인덱스를 사용해서 레코드에 접근하는 방법,
-  전문 검색할 컬럼에 인덱스가 있어야 한다.
- ```"MATCH ... AGAINST ..."``` 구문을 사용해서 실행된다.
  - MATCH(컬럼명) AGAINST('검색어1 검색어2')
```sql
EXPLAIN
SELECT *
FROM employee_name
WHERE MATCH(first_name, last_name) AGAINST('Facello');
```
 ![image](https://user-images.githubusercontent.com/48278519/150077554-48ec4970-8b13-4a39-be9b-cc221add4155.png)


### #ref_or_null 
- **ref 접근 방식에서 NULL 비교가 추가된 형태**
```sql
EXPLAIN
SELECT * FROM titles WHERE to_date='1985-03-31' OR to_date IS NULL;
```
![image](https://user-images.githubusercontent.com/48278519/150077712-a66757f2-ae72-430e-b080-95757434951c.png)


### #unique_subquery
- where 조건에 **IN subquery 형태를 가지면서 서브 쿼리에서 중복되지 않는 유니크한 값만 반환될 때** 사용
```sql
EXPLAIN
SELECT * FROM departments WHERE dept_no IN (
  SELECT dept_no FROM dept_emp WHERE emp_no=10001);
```
![16](https://user-images.githubusercontent.com/48278519/150078741-8578072d-b92f-43d1-a3e5-e509acec8dc6.JPG)


### #index_subquery
- where 조건에 IN subquery 형태를 가질때 사용할 수 있으며, 반환되는 값이 유니크하지 않은 경우에 인덱스를 이용하여 중복을 제거하는 경우
- ```IN subquery에서 subquery가 중복된 값을 반환할 수는 있지만, 중복된 값을 인덱스를 이용해 제거할 수 있을 때``` index_subquery 접근 방법이 사용된다.
- 참고로, 위의 unique_subquery에서는 반환 값 자체에 중복이 없으므로 별도의 중복 제거 작업이 필요하지 않음

다음 쿼리에서 IN 연산자 내의 서브 쿼리는 dept_emp 테이블을 dept_no로 검색한다. 
dept_emp 테이블의 프라이머리 키가 (dept_no + emp_no)로 만들어져 있으므로 서브 쿼리는 프라이머리 키의 dept_no 칼럼을 'd001'부터 'd003'까지 읽으면서 dept_no 값만 가져온다.

```sql
EXPLAIN
SELECT * FROM departments WHERE dept_no IN (
  SELECT dept_no FROM dept_emp WHERE dept_no BETWEEN 'd001' AND 'd003');
 ```
![17](https://user-images.githubusercontent.com/48278519/150078754-c10586c6-722b-4536-b2e4-ba18d3a39c2f.JPG)


<br>
  
### #range
- **테이블 내의 연속된 데이터 범위를 조회**하는 유형 (인덱스 레인지 스캔 형태의 접근 방법)
- 주로 ```<, >, IS NULL, BETWEEN, IN, LIKE``` 등의 연산자를 이용해 인덱스를 검색할 때 사용
-**참고)** 통상적으로 '인덱스 스캔이다' 라고하면 range, const, ref를 묶어서 지칭하며, 스캔할 범위가 넓으면 성능 저하의 요인이 될 수 있다.
 
```sql
EXPLIAN
SELECT dept_no FROM dept_emp WHERE dept_no BETWEEN 'd001' AND 'd003';
```
![image](https://user-images.githubusercontent.com/48278519/150078999-bfe9d444-f8a7-4415-8064-ea187860bdc5.png)

<br>
  
### #index_merge
- **2개 이상의 인덱스를 이용해 각각의 검색결과를 만든 후 결과를 합치는 접근 방식**
- 여러 인덱스를 읽어야 하므로 일반적으로 range 접근 방식보다 효율성이 떨어진다.
- AND와 OR 연산이 복잡하게 연결된 쿼리에서는 제대로 최적화되지 못할 때가 많다.
- 전문 검색 인덱스를 사용하는 쿼리에서는 index_merge가 적용되지 않는다.
- index_merge 접근 방식으로 처리된 결과는 항상 2개 이상의 집합이 되기 때문에 그 두 집합의 교집합이나 합집합 또는 중복 제거와 같은 부가적인 작업이 더 필요하다.
  
아래 쿼리는 OR로 연결된 두 개 조건이 각각 다른 인덱스를 최적으로 사용할 수 있는 조건이다.<br>
 그래서 옵티마이저는 "emp_no BETWEEN 10001 AND 11000" 조건은 employees 테이블의 프라이머리 키를 이용해 조회하고, 
 "first_name='Smith'" 조건은 ix_firstname 인덱스를 이용해 조회한 후 두 결과를 병합하는 형태로 처리한다.
```sql
EXPLAIN
SELECT * FROM employees
WHERE emp_no BETWEEN 10001 AND 11000
	OR first_name='Smith';
 ```
![image](https://user-images.githubusercontent.com/48278519/150079176-e23131ed-5eaa-46bb-9bab-cde986d5997d.png)

<br>
  
### #index 
  
- **인덱스를 처음부터 끝까지 읽어야 하는 경우에 쓰는 접근 방식**(인덱스 풀 스캔). 다음 두 가지 조건에서 발생한다. 
  - 인덱스 스캔(range, const, ref) 불가능한 경우 + 인덱스에 포함된 컬럼만으로 처리할 수 있는 쿼리인 경우(굳이 데이터파일 읽지 않아도 되는 경우)
  - 인덱스 스캔(range, const, ref) 불가능한 경우 + 인덱스를 이용해 정렬이나 그룹핑 작업이 가능한 경우(정렬 작업을 피할 수 있는 경우)
- 테이블을 처음부터 끝까지 읽는 풀 테이블 스캔 방식과 비교했을 때 비교하는 레코드 건수는 같지만, 일반적으로 인덱스는 데이터 파일 전체보다는 크기가 작아서 풀 테이블 스캔보다는 효율적이다.
  
다음 쿼리는 아무런 WHERE 조건이 없으므로 range나 const 또는 ref 접근 방식을 사용할 수 없다. 하지만 정렬하려는 칼럼은 인덱스(ux_deptname)가 있으므로  index 접근 방식이 사용된 예제다.

```sql
EXPLAIN
SELECT * FROM departments ORDER BY dept_name DESC LIMIT 10;
```

### #ALL
- 풀 테이블 스캔. 테이블을 처음부터 끝까지 전부 읽어서 불필요한 레코드를 제거(체크 조건이 존재할 때)하고 반환
- 위 접근 방법들로 처리할 수 없을 때 가장 마지막에 선택되는 가장 비효율적인 방법
- InnoDB도 풀 테이블 스캔이나 인덱스 풀 스캔과 같은 대량의 디스크 I/O를 유발하는 작업을 위해 한꺼번에 많은 페이지를 읽어들이는 기능을 제공한다. 
  InnoDB에서는 이 기능을 "리드 어헤드(Read Ahead)"라고 하며, 한 번에 여러 페이지를 읽어서 처리할 수 있다. 


## Type 칼럼의 주의 대상
### ALL, index
index는 인덱스 풀 스캔을 의미하며, ALL은 풀 테이블 스캔을 의미한다.<br>
둘 다 전체 레코드를 대상으로 하는 작업 방식이라서 빠르게 결과를 가져오기는 어렵다.<br>
특히 일반적인 OLTP 환경에 적합한 접근 방식은 아니므로 새로운 인덱스를 추가하거나 쿼리의 요건을 변경해서 이러한 적근 방법을 제거하는 것이 좋다.

<br>
  
## 6) Possible_keys  컬럼
- 옵티마이저가 최적화하고자 사용할 수 있는 인덱스 목록
- 실제 사용한 인덱스가 아닌, 사용할 수 있는 후보군의 기본 키/인덱스 목록만 보여주므로 튜닝의 효용성은 없다.

<br>
  
## 7) key  컬럼
- possible_keys 컬럼에서 보여진 후보 인덱스 목록 중 **실제 사용된 인덱스**
- 인덱스 사용 못했을 경우는 NULL로 표기된다.
- 비효율적인 인덱스를 사용했거나 인덱스를 사용하지 않았다면 SQL 튜닝 대상이 될 수 있다.
-  PRIMARY인 경우에는 프라이머리 키를 사용한다는 의미하며, 그 이외의 값은 모두 테이블이나 인덱스를 생성할 때 부여했던 고유 이름이다.
 
<br>

## 8) key_len  컬럼
  
- **사용한 인덱스의 byte수 **
- 실무에서 인덱스를 단일 컬럼으로 만들기 보단 다중 컬럼으로 만들어지는 경우가 더 많다. 이 때 key_len은```다중 컬럼 인덱스 중에서 몇 바이트까지 사용했는지``` 알려주므로 각 인덱스 컬럼에 할당된 바이트를 알 수 있으니 몇 개의 인덱스 컬럼이 사용되었는지를 추산할 수 있다.
- dept_emp 테이블의 프라이머리 키가 (dept_no + emp_no)로 두 개의 칼럼으로 만들어 졌고, dept_no는 CHAR(4), emp_no는 integer이라 할 때,<br>
  조회 결과 아래와 같이 key_len 칼럼의 값이 12로 표시되었다면 테이블의 키 중 dept_no 까지만 쓰였다는 것을 의미한다. <br>
  ( UTF8 기준 문자 하나가 차지하는 공간은 3바이트로 계산할 때 4*3 = 12이므로 프라이머리 키에서 앞쪽 12바이트만 유효하게 사용했다)
```sql
EXPLAIN
SELECT * FROM dept_emp WHERE dept_no='d005';
```
![image](https://user-images.githubusercontent.com/48278519/150079874-b5fdc16a-7792-4ab9-b97c-6e028ace0d8b.png)

<br>
  
## 9) ref 컬럼
  
- reference의 약자로, **어떤 컬럼이 액세스 조건(참조 조건)에 사용되었는지**를 알려주는 정보
- 상수 값을 지정했다면 ref 칼럼의 값은 const로 표시되고, 다른 테이블의 칼럼값이면 그 테이블 명과 칼럼 명이 표시된다. 
- 단순 컬럼이 아니라 어떤 가공된 컬럼이 사용될 때는  "func"으로 나타난다.
- MySQL 서버가 내부적으로 값을 변환해야 할 때도 ref 칼럼에는 "func"가 출력된다. ```가능하다면 MySQL 서버가 이런 변환을 하지 않아도 되도록 조인 칼럼의 타입은 일치시키는 편이 좋다.```
(EX. 문자집합이 일치하지 않는 두 문자열 칼럼을 조인하는 경우, 숫자 타입의 칼럼과 문자열 타입의 칼럼으로 조인하는 경우) 

- **예제 1**
  - 2개의 ID가 똑같이 1로 출력되었으므로, 사원 테이블과 직급 테이블이 조인이 되었음을 알 수 있으며, 드리븐  테이블인 직급 테이블의 데이터에 접근하면, 사원 번호로 데이터를 검색함을 확인할 수 있다.
![15](https://user-images.githubusercontent.com/48278519/150080855-f3d495dc-f065-4da0-a573-18b2e61577ca.png)


-**예제 2**
   -  dept_emp 테이블을 읽어서 de.emp_no 값에서 1을 뺀 값으로 employees 테이블과 조인하고 있다. 이 쿼리의 실행 계획에서는 ref 값이 "func"라고 표시되는 것을 확인할 수 있다.
```sql  
EXPLAIN
SELECT *
FROM employees e, dept_emp de
WHERE e.emp_no=(de.emp_no-1);
```
 ![18](https://user-images.githubusercontent.com/48278519/150080876-83494ae9-59eb-4bd1-866e-2c37af0887e7.JPG)

<br>
  
## 9) rows  컬럼
- 옵티마이저가 비용을 산정하기 위해 얼마나 많은 레코드를 읽고 비교해야하는지 예측해본 레코드의 수, 수시로 변동되는 통계정보를 참고하여 산출하는 값으로 수치가 정확하진 않다. 또한 최종 출력될 행 수가 아니라는 점에 유의해야 한다.
- 주의 사항
  - 쿼리가 실제 가져오는 레코드 수보다 훨씬 더 큰 값이 Rows 칼럼에 표시되는 경우에는 쿼리가 인덱스를 정상적으로 사용하고 있는지, 그리고 그 인덱스가 충분히 작업 범위를 좁혀 줄 수 있는 칼럼으로 구성됐는지 검토해보는 것이 좋다. 
  -  LIMIT가 포함된 쿼리라 하더라도 LIMIT의 제한은 Rows 칼럼의 고려 대상에서 제외된다. "LIMIT 1"로 1건만 SELECT 하는 쿼리라 하더라도 Rows 칼럼에는 훨씬 큰 수치가 표현될 수도 있으며, 성능상 아무런 문제가 없고 최적화된 쿼리일 수도 있다.

 
# 3. 정리
  
 
**1.** 실행계획 (MySQL Explain)이란 DB가 테이터를 찾아가는 일련의 과정을 사람이 알아보기 쉽게 DB 결과 셋으로 보여주는 것이며, MySQL Explain을 활용하면 기존의 쿼리를 튜닝할 수 있을 뿐만 아니라 성능 분석, 인덱스 전략 수립 등과 같이 성능 최적화에 대한 전반적인 업무를 처리할 수 있다.

 <br>
  
**2.** 실행계획은 EXPLAIN 키워드를 붙여서 실행하며, 테이블 형식으로 결과가 보여진다. (MySQL 8.0부터는 JSON이나 TREE로도 볼 수 있다)

 <br>
  
**3.** 각 실행계획의 컬럼은 다음과 같다

  ![16](https://user-images.githubusercontent.com/48278519/150087373-4b1272ec-4c4a-45fc-9d98-273b6eddda2a.png)

<br>
 
 **4.** select_type 컬럼은 select 문의 유형을 출력하는 컬럼이다.
    - 그 중 "DEPENDENT" 키워드를 포함하는 서브 쿼리에는 외부 쿼리에 의존적이므로 외부 쿼리보다 먼저 실행될 수가 없다. 
    - 따라서 select_type에 "DEPENDENT" 키워드가 포함된 서버 쿼리는 비효율적인 경우가 많다.

<br>
  
**5.**  SUBQUERY는 바깥쪽(Outer)의 영향을 받지 않으므로 처음 한 번만 실행해서 그 결과를 캐시하고 필요할 때 캐시된 결과를 이용하고, DEPENDENT SUBQUERY는 의존하는 바깥쪽(Outer) 쿼리의 칼럼의 값 단위로 캐시해두고 사용한다. 하지만 다음의 3가지 경우에선 캐시를 재사용하지 못하므로 유의해야 한다.
  - 사용자 변수가 서브 쿼리에 들어갔거나
  -  UUID(), RAND() 같이 결과값이 호출할 때마다 변경되는 함수가 서브 쿼리에 들어갔거나
  - NOT-DETERMINISTIC 속성의 stored function이 서브 쿼리에 들어간 경우

따라서 이런 실행 계획이 사용된다면 ```사용자 변수를 제거하거나 다른 함수로 대체해서 사용 가능할지 검토```해 보는 것이 좋다.

<br>
  
**6.** type컬럼은 MySQL 서버가 각 테이블의 레코드를 어떤 방식으로 읽었는지 정보를 나타내는 컬럼으로, 실행 계획이 적절하게 인덱스를 참조했는지를 확인하는데 가장 핵심인 컬럼이다. <br>
그 중 대표적으로 효율적인 참조 방법 3가지로 const, eq_ref,ref가 있다.

- **const** : 조인의 순서와 관계없이 프라이머리 키나 유니크 키의 모든 칼럼에 대해 동등(Equal) 조건으로 검색(반드시 1건의 레코드만 반환)
- **eq_ref** : 조인에서 첫 번째 읽은 테이블의 칼럼값을 이용해 두 번째 테이블을 프라이머리 키나 유니크 키로 동등(Equal) 조건 검색(두 번째 테이블은 반드시 1건의 레코드만 반환)
- **ref** : 조인의 순서와 인덱스의 종류에 관계없이 동등(Equal) 조건으로 검색(1건의 레코드만 반환된다는 보장이 없어도 됨)
  
 이 세 가지 접근 방식 모두 WHERE 조건절에 사용되는 비교 연산자는 동등 비교 연산자이어야 한다는 공통점이 있다. <br>
또한 세 가지 모두 매우 좋은 접근 방법으로 인덱스의 분포도가 나쁘지 않다면 성능상의 문제를 일으키지 않는 접근 방법이다. 쿼리를 튜닝할 때도 이 세 가지 접근 방법에 대해서는 크게 신경 쓰지 않고 넘어가도 무방하다.

<br>
  
**7**. ref 컬럼은 reference의 약자로, 어떤 컬럼이 액세스 조건(참조 조건)에 사용되었는지를 알려주는 정보이다.<br>
상수 값을 지정했다면 ref 칼럼의 값은 const로 표시되고, 다른 테이블의 칼럼값이면 그 테이블 명과 칼럼 명이 표시된다. 하지만 가공된 컬럼이 사용될 때는  "func"으로 표현되는데, 가능하다면 MySQL 서버가 가공 및 변환을 하지 않아도 되도록 조인 칼럼의 타입은 일치시키는 편이 좋다.


  
  ## 출처
- 업무에 바로 쓰는 sql튜닝
  
- https://jeong-pro.tistory.com/243 

- https://12bme.tistory.com/168

- https://velog.io/@jsj3282/24.-MySQL-%EC%8B%A4%ED%96%89-%EA%B3%84%ED%9A%8D-%EC%8B%A4%ED%96%89-%EA%B3%84%ED%9A%8D-%EB%B6%84%EC%84%9D2

- https://jeong-pro.tistory.com/243 [기본기를 쌓는 정아마추어 코딩블로그]

- https://velog.io/@jsj3282/23.-MySQL-%EC%8B%A4%ED%96%89-%EA%B3%84%ED%9A%8D-%EC%8B%A4%ED%96%89-%EA%B3%84%ED%9A%8D-%EB%B6%84%EC%84%9D
