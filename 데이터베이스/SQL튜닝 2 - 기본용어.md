# 1.DB 오브젝트 스캔 종류    

테이블 스캔: 인덱스를 거치지 않고 테이블의 데이터로 직접 접근

인덱스 스캔: 인덱스를 통해 테이블 데이터에 접근

## 1) 테이블 스캔 / 테이블 풀 스캔 (Table Full Scan)
- 인덱스를 거치지 않고 테이블 데이터에 직행하여 처음과 끝까지 스캔하는 방식
- WHERE 조건문을 기준으로 활용할 인덱스가 없는 경우, 전체 데이터 대비 대량의 데이터가 필요할 때 사용
- 대량의 데이터에 접근할 때 테이블 풀 스캔을 쓰면, 인덱스를 통한 랜덤 액서스가 발생하지 않아 이점이 있음
![1](https://user-images.githubusercontent.com/48278519/149135654-0c6f21e6-b28d-4604-b476-7e40f2eaa375.png)

<br>
 
## 2) 인덱스 스캔
### 인덱스 풀 스캔 (Index Full Scan)
- 테이블에 접근하지 않고, 인덱스 내의 열만을 참조하여 처음부터 끝까지 스캔하여 데이터에 접근하는 방식
- 인덱스는 테이블의 일부 데이터를 사용하므로, 테이블 풀 스캔보다는 유리함.
- B+ 트리에서 첫 번째 leaf 노드를 수직 탐색 후 나머지 leaf 노드에 대해 순차적으로 전체 탐색을 진행
![2](https://user-images.githubusercontent.com/48278519/149135666-1e901f64-ec54-4277-b294-02d393ab5106.png)

![3](https://user-images.githubusercontent.com/48278519/149135674-845ea037-322e-4143-8b6e-7c095d3d0ec2.png)


EX)
```sql
CREATE TABLE `table1` (
  `table1_id` int(11) NOT NULL
  PRIMARY KEY (`table1_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

EXPLAIN SELECT COUNT(*) FROM table1;
```
![4](https://user-images.githubusercontent.com/48278519/149135709-700b38eb-18a7-4357-ae8c-9de24de44c33.png)

(WHERE 절에 조건이 없으므로 const, ref, range 등의 접근방식으로 인덱스를 사용할 수 없고 count 쿼리를 수행하기위해 전체의 데이터를 읽어야하므로 Index full scan 으로 수행)

<br>

### 인덱스 범위 스캔 (Index Range Scan)
- SQL문에 BETWEEN ~ AND, LIKE, < 등을 사용했을 때, 인덱스를 범위 기준으로 스캔한 뒤, 스캔 결과를 토대로 테이블에 접근
- 좁은 범위를 대상으로 수행할 경우는 이점이 있지만, 넓은 범위를 대상으로는 비효율적인 방식
- 인덱스를 통해 B+Tree를 수직 탐색 후 leaf 노드레벨에서 필요한 범위까지 탐색하는 방식
![5](https://user-images.githubusercontent.com/48278519/149135740-005d30b3-c7e4-42c0-9f07-16369739e9d6.png)
![6](https://user-images.githubusercontent.com/48278519/149135748-835cd41f-5515-4eee-b680-6be5cd59b6fd.png)

EX) 
```sql
CREATE TABLE `table1` (
  `table1_id` int(11) NOT NULL
  PRIMARY KEY (`table1_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
EXPLAIN SELECT * FROM table1 WHERE table1_id > 1;
```
![7](https://user-images.githubusercontent.com/48278519/149135757-bbef9a42-c43b-4e2d-977a-9f54312befa3.png)

<br>

### 인덱스 고유 스캔 (Index Unique Scan)
- WHERE문에 = 을 사용하였을 때, 기본키나 고유 인덱스로 테이블에 접근하는 방식
- 인덱스를 사용하는 방식 중 가장 효율적인 스캔 방식

![8](https://user-images.githubusercontent.com/48278519/149135795-5567e5f3-31c2-4664-a2b2-e7b36f134643.png)
![9](https://user-images.githubusercontent.com/48278519/149135812-535368db-b75a-4cc9-b9a9-212ab6b56010.png)

EX)
```sql
EXPLAIN SELECT * FROM table1 WHERE table1_id = 25;
```
 <br>
 
### 인덱스 루스 스캔 (Index Loose Scan)
- 인덱스에서 필요한 부분만 선택하여 스캔하는 방식
- 인덱스 범위 스캔처럼 넓은 범위에 전부 접근하지 않고, WHERE 조건문 기준으로 필요한 데이터와 불필요한 데이터를 구분한 뒤 불필요한 인덱스 키는 무시
- 보통 GROUP BY, MAX(), MIN() 함수 포함 시 동작
- 
![10](https://user-images.githubusercontent.com/48278519/149135824-82b965a7-02ef-4fa4-a9ab-53bc316e9ef4.png)


### 인덱스 병합 스캔 (Index Merge Scan)
- 테이블 내에 생성되 인덱스를 통합하여 스캔하는 방식
- WHERE문의 조건 열이 서로 다른 인덱스에 존재할 때 사용됨.
- 인덱스 병합 스캔은 물리적으로 존재하는 개별 인덱스를 각각 접근하므로 인덱스에 접근하는 시간이 오래 걸린다. 따라서 보통 별개로 생성된 인덱스를 하나의 인덱스로 통합하여 튜닝을 수행함
![11](https://user-images.githubusercontent.com/48278519/149135834-403fdba6-9afb-4ef7-b576-38d1f7850441.png)

 <br>
 
# 2. 디스크 접근 방식          
- MySQL은 원하는 데이터를 찾기 위해 데이터가 저장된 스토리지(storage)의 페이지(page)에 접근한다. 여기서 페이지란 데이터를 검색하는 최소 단위로, 페이지 단위로 데이터를 읽고 쓸 수 있다. 이 때 서로 연결된 페이지를 순서대로 읽을 수도 있고, 원하는 페이지를 임의로 열어보면서 읽을 수 있는데 전자를 시퀀셜 액세스, 후자를 랜덤 액세스라고 한다.

 
## 1) 시퀀셜 액세스
- 물리적으로 인접한 페이지를 순차적으로 읽는 방식
- 데이터를 찾고자 이동하는 디스크 헤더의 움직임을 최소화하여 작업시간과 리소스 점유 시간을 줄일 수 있음.
- 테이블 풀 스캔이 동작하는 방식 

![12](https://user-images.githubusercontent.com/48278519/149135857-48c0f3fc-98e1-4710-9ac8-b0bd2d9ac120.png)

<br>

## 2) 랜덤 액세스
- 물리적으로 떨어진 페이지를 임의로 접근하는 방식
- 디스크 헤더에 많은 움직임을 요구하여, 데이터 접근 시간이 오래 걸림.
- 접근 범위를 줄이는 방식으로 튜닝이 필요할 수 있음.

![13](https://user-images.githubusercontent.com/48278519/149135880-34f3e7d7-73ca-4b37-8252-301a95fff157.png)

 <br>
 
# 3. 조건 유형            
- SQL문은 where절 조건문을 기준으로 디스크에 접근한다. 
이 때 필요한 데이터에 액세스하는 조건문으로 데이터를 가져오고, 가져온 데이터에서 다시 한 번 출력할 데이터만 추출한다. 맨 처음 디스크에서 데이터를 검색하는 조건을 액세스 조건, 디스크에서 가져온 데이터에서 추가로 추출하거나 가공 / 연산하는 조건을 필터 조건이라 한다.
 

## 1) 액세스 조건
- 디스크에 있는 데이터에 **어떻게 접근할 것인지 다루는 조건.**
- 옵티마이저는 where 절의 특정 조건문을 이용해 소량의 데이터를 가져오고, 인덱스를 통해 시간 낭비를 줄이는 조건절을 선택함
- 예시 : ID = 1 과 code = 'A' 조건이 있지만, ID열로 생성된 인덱스를 활용해서 TABLE2 테이블의 일부 데이터에 접근(ID = 1 조건문을 액세스 조건으로 삼음)

![14](https://user-images.githubusercontent.com/48278519/149135889-39a84555-a676-42a1-9b08-b2942005d49b.png)


## 2) 필터 조건
- 액세스 조건을 이용해 **추가로 불필요한 데이터를 제거하거나 가공하는 조건**
- 액세스 조건으로 가져온 데이터를 대상으로 필터 조건에 따라 필터링할 데이터가 없다면 훌륭한 SQL문이고, 필터 조건으로 필터링 되어 제거된 데이터가 다수 존재 시 비효율적인 SQL이라고 판단 가능 
- ( 스토리지엔진 -> MySQL 엔진으로 데이터를 전달하는 오버헤드가 있으며, 필터 조건으로 제거될 데이터라면 스토리지 엔진의 데이터에 접근하는 과정에서 같이 제외되는 편이 성능적으로 효율적이기 때문)
- 필터 조건으로 제거되는 데이터의 비율은 실행 계획의 filtered 항목에서 확인 

![15](https://user-images.githubusercontent.com/48278519/149135898-92cd73a5-6341-4cae-abcf-cf1a60f20822.png)

 <br>
 
# 4.응용 용어        

##  1) 선택도(selectivity) 
- **테이블의 특정 열을 기준으로 해당 열의 조건절 (WHERE 절 조건문)에 따라 선택되는 데이터 비율**
- 해당 열에 중복되는 데이터가 많다면 '선택도가 높다' / 해당 열에 중복되는 데이터가 적으면 '선택도가 낮다'
- 낮은 선택도가 대용량 데이터에서 원하는 데이터만 골라 낼 수 있기 때문에 낮은 선택도를 가지는 열은 데이터를 조회하는 SQL 문에서 원하는 데이터를 빨리 찾기 위한 인덱스 열을 생성할 때 주요 고려대상 
- ```선택도 = 선택한 데이터 건수 ÷ 전체 데이터 건수```
- ```일반화된 선택도 = 1 ÷ DISTINCT(COUNT 열 명)```

ex) 학생 테이블로 기본 키인 학번 열과 이름, 성별이라는 일반 열로 구성되어 있고, 총 100건의 데이터가 저장되어 있을 때

```sql
학번 열의 선택도 

-- 전체 데이터 건수 구하기
SELECT COUNT(*) 
FROM 학생; => 100

 -- 학번 데이터에서 중복을 제외한 개수 구하기 
SELECT COUNT(DISTINCT 학번)
FROM 학생;  => 100

-- 선택도 구하기
SELECT 1 / COUNT(DISTINCT 학번)
FROM 학생;  => 0.01
 ```

성별의 '여' 조건에 대한 선택도 : 
```sql
-- 전체 데이터 건수 구하기
SELECT COUNT(*) 
FROM 학생; => 100

-- 성별 데이터에서 중복을 제외한 개수 구하기
SELECT COUNT(DISTINCT 성별)  
FROM 학생;   =) 2

 -- 성별 열의 선택도 구하기
SELECT 1 / COUNT (DISTINCT 성별)
FROM 학생;   => 0.5
 ```

## 2) 카디널리티(cardinality) 

- **'하나의 데이터 유형으로 정의되는 데이터 행의 개수'**
- 전체 데이터에 접근한 뒤 출력될 것이라 예상되는 데이터 건수를 의미. 전체 행에 대한 특정 열의 중복 수치를 나타내는 지표로 자주 활용됨
중복도가 높으면 -> 카디널리티가 낮고,  중복도가 낮으면 -> 카티널리티가 높다
- 카디널리티 = 전체 데이터 건수 x 선택도
ex) 전체 데이터가 100건인 테이블에서 기본키가 학번인 열을 대상으로 카디널리티를 구한다고 가정하자.

학번 열의 카디널리티는 100 × 0.01 = 1건 (모든 학번의 데이터값이 고유한 만큼 1건의 데이터만 출력되리라 예측 가능)

ex2)  일상생활에서의 카디널리티 적용 사례
- 주민등록번호: 카디널리티 높음
- 이름 카디널리티 중간
- 성별: 카디널리티 낮음

 <br>
 
# 3. SQL HINT

## 정의 
- 일종의 지시 구문이며,  **SQL에 포함되어 쓰여져 Optimizer의 실행 계획을 원하는대로 바꿀 수 있게 해주는 것**
- Optimizer라고 해서 항상 최선의 실행계획을 수립할 수는 없으므로 테이블이나 인덱스의 잘못된 실행 계획을 개발자가 직접 바꿀 수 있도록 도와주는 것이 힌트이다.

## 특징
- 실행 계획을 제어
- 힌트 때문에 에러가 발생하지는 않음
- 힌트의 문법이 올바르더라도 옵티마이저가 비효율적이라고 예측하면 사용자가 작성한 힌트는 무시될 수 있다.

 ![16](https://user-images.githubusercontent.com/48278519/149135962-6efd5260-6815-401c-bb38-568d0fd553bf.png)

예를 들어, 이름 열에 대한 학생_idx01 인덱스, 전공코드 열에 대한 학생_idx2 인덱스, 학생 테이블이 존재한다. 

```sql
SELECT 학번, 전공 코드
FROM 학생
WHERE 이름 = '유재석';
```

위의 쿼리 실행 시, '유재석'이라는 이름의 학생 정보를 가져오기 때문에 학생_IDX01인덱스를 이용하면 원하는 데이터를 빨리 찾을 수 있을 것
=> **학생_IDX01 인덱스를 활용하여 찾아달라는 힌트를 작성할 수 있다.**

```sql
SELECT 학번, 전공코드
FROM 학생 /*! USE INDEX (학생_IDX01) */
WHERE 이름 = '유재석';

--- 주석 표기 없이 작성
SELECT 학번, 전공코드
FROM 학생 USE INDEX (학생_IDX01)
WHERE 이름 = '유재석';
```

## Hint의 종류

### 1) 인덱스 힌트
- 복잡한 쿼리의 경우 옵티마이저가 엉뚱한 인덱스를 사용하거나, 최적의 순서로 처리하지 않을 수 있다.
- 이런 경우, Mysql에서 제공하는 인덱스 힌트를 쓰면 강제적으로 할당한 Index를 이용하여 쿼리가 실행이 된다.
- JPA(hibernate)에서 사용이 불가능하기 때문에 JdbcTemplate 등을 이용하여 Native Query로 활용해야 된다.

 **사용 방법**
```sql
TABLE_NAME [[AS] ALIAS] INDEX_HINT INDEX_LIST

INDEX_LIST:
    USE {INDEX|KEY}
      [FOR {JOIN|ORDER BY|GROUP BY}] (INDEX_LIST)
  | IGNORE {INDEX|KEY}
      [FOR {JOIN|ORDER BY|GROUP BY}] (INDEX_LIST)
  | FORCE {INDEX|KEY}
      [FOR {JOIN|ORDER BY|GROUP BY}] (INDEX_LIST)

INDEX_LIST:
    INDEX_NAME , INDEX_NAME ...
```
 
- **USE 키워드** : 특정 인덱스를 사용하도록 권장
- **IGNORE 키워드** : 특정 인덱스를 사용하지 않도록 지정
- **FORCE 키워드** : USE 키워드와 동일한 기능을 하지만, 옵티마이저에게 보다 강하게 해당 인덱스를 사용하도록 권장
- **USE INDEX FOR JOIN** : JOIN 키워드는 테이블간 조인뿐 아니라 레코드 검색하는 용도까지 포함
- **USE INDEX FOR ORDER BY** : 명시된 인덱스를 ORDER BY 용도로만 사용하도록 제한
- **USE INDEX FOR GROUP BY** : 명시된 인덱스를 GROUP BY 용도로만 사용하도록 제한

**예시**
```sql
SELECT * 
FROM TABLE1 
  USE INDEX (COL1_INDEX, COL2_INDEX)
WHERE COL1=1 AND COL2=2 AND COL3=3;

SELECT * 
FROM TABLE2 
  IGNORE INDEX (COL1_INDEX)
WHERE COL1=1 AND COL2=2 AND COL3=3;

SELECT * 
FROM TABLE3
  USE INDEX (COL1_INDEX)
  IGNORE INDEX (COL2_INDEX) FOR ORDER BY
  IGNORE INDEX (COL3_INDEX) FOR GROUP BY
WHERE COL1=1 AND COL2=2 AND COL3=3;
```

### 2) 옵티마이저 힌트

- 옵티마이저를 더 세밀하게 선택적으로 제어해야 할 땐, 옵티마이저 제어를 원하는 부분을 지정할 수 있는 옵티마이저 힌트를 사용한다.
- 명령문의 한 테이블에 대한 최적화를 활성화하고 다른 테이블에 대한 최적화를 비활성화할 수 있다
- 또한 명령문 내의 옵티마이저 힌트는 optimizer_switch 시스템 변수보다 우선시 되어 적용되며, 옵티마이저 힌트는 다양한 범위 수준에서 적용된다.

- 적용 가능한 범위 수준
  - 전역: 힌트가 전체 문에 영향을 줌
  - 쿼리 블록: 힌트가 명령문 내의 특정 쿼리 블록에만 영향을 줌
  - 테이블: 힌트가 쿼리 블록 내의 특정 테이블에민 영향을 줌
  - 인덱스: 힌트가 테이블 내의 특정 인덱스에만 영향을 줌
  
 **사용 방법**

옵티마이저 힌트는 /+ .... /주석 내에 지정해야 한다.
|hint|설명|적용 범위|
|---|---|---|
|BKA, NO_BKA|	일괄 처리된 키 액세스 조인 처리에 영향|	쿼리 블록, 테이블|
|BNL, NO_BNL|	MySQL 8.0.20 이전: 블록 중첩-루프 조인 처리, MySQL 8.0.18 이상: 해시 조인 최적화, MySQL 8.0.20 이상: 해시 조인 최적화에만 영향|	쿼리 블록, 테이블|
|DERIVED_CONDITION_PUSHDOWN, NO_DERIVED_CONDITION_PUSHDOWN|	구체화된 파생 테이블에 대한 파생 조건 푸시다운 최적화 사용 또는 무시(MySQL 8.0.22에 추가)|쿼리 블록, 테이블|
|GROUP_INDEX, NO_GROUP_INDEX| GROUP BY 작업(MySQL 8.0.20에 추가)에서 인덱스 검색에 대해 지정된 인덱스를 사용하거나 무시 |인덱스|
|HASH_JOIN, NO_HASH_JOIN| 해시 조인 최적화에 영향 (MySQL8.0.18만 해당) |쿼리 블록, 테이블|
|INDEX, NO_INDEX| JOIN_INDEX, GROUP_INDEX 및 ORDER_INDEX의 조합으로 작동하거나 NO_JOIN_INDEX, NO_GROUP_INDEX 및 NO_ORDER_INDEX(MySQL 8.0.20에 추가)의 조합으로 작동| 인덱스|
|INDEX_MERGE, NO_INDEX_MERGE|인덱스 병합 최적화에 영향|테이블, 인덱스|
|JOIN_INDEX, NO_JOIN_INDEX| 모든 액세스 방법에 대해 지정된 인덱스 또는 인덱스를 사용하거나 무시(MySQL 8.0.20에 추가) |인덱스|
|JOIN_FIXED_ORDER| FROM절에 지정된 순서대로(FIXED) 테이블을 조인하도록 지시 |쿼리 블록|
|JOIN_ORDER|	가능하다면 힌트에 지정된 순서대로 조인하도록 지시|	쿼리 블록|
|JOIN_PREFIX|	가장 먼저 조인을 시작 할 테이블 지정|	쿼리 블록|
|JOIN_SUFFIX|	가장 마지막으로 조인 할 테이블 지정|	쿼리 블록|
|MAX_EXECUTION_TIME|	구문 실행 시간 제한|	전역 범위|
|MERGE, NO_MERGE|	외부 쿼리 블록으로 병합되는 파생 테이블/뷰에 영향| 테이블|
|MRR, NO_MRR|	다중 범위 읽기 최적화에 영향|	테이블, 인덱스|
|NO_ICP|	인덱스 조건 푸시다운 최적화에 영향|	테이블, 인덱스|
|NO_RANGE_OPTIMIZATION|	범위 최적화에 영향|	테이블, 인덱스|
|ORDER_INDEX, NO_ORDER_INDEX|	행을 정렬하기 위해 지정된 인덱스 또는 인덱스 사용하거나 또는 무시(MySQL 8.0.20에 추가)| 인덱스|
|QB_NAME| 쿼리 블록에 이름 할당|	쿼리 블록|
|RESOURCE_GROUP| 구문을 실행하는 동안 리소스 그룹 설정|	전역 범위|
|SEMIJOIN, NO_SEMIJOIN|	Semijoin 전략에 영향, MySQL 8.0.17부터는 anti조인에도 적용|	쿼리 블록|
|SKIP_SCAN, NO_SKIP_SCAN| 스킵 검색 최적화에 영향 |테이블, 인덱스|
|SET_VAR| 구문을 실행하는 동안 변수 설정| 전역 범위|
|SUBQUERY| 구체화에 영향, IN-to-EXISTS 하위 쿼리 전략 |쿼리 블록|

옵티마이저 힌트는 SELECT, UPDATE, INSERT, REPLACE, DELETE문에서 아래와 같이 사용할 수 있다.

```sql
SELECT /*+ HINT */ ...
INSERT /*+ HINT */ ...
REPLACE /*+ HINT */ ...
UPDATE /*+ HINT */ ...
DELETE /*+ HINT */ ...
```

### 3) 조인 순서 최적화 힌트

- MySQL 8.0은 이후 버전에서 제공하는 Optimizer hint 중 하나로, 옵티마이저가 테이블을 조인하는 순서에 영향을 준다.

**사용 방법**

```sql
HINT_NAME([@query_block_name])
HINT_NAME([@query_block_name] TABLE_NAME [, tbl_name] ...)
HINT_NAME(TABLE_NAME[@query_block_name] [, TABLE_NAME[@query_block_name]] ...)
```

- HINT_NAME에 올 수 있는 조인 순서 최적화 힌트는 4가지가 있다.
  - **JOIN_FIXED_ORDER**: FROM절에 지정된 순서대로(FIXED) 테이블을 조인하도록 지시 (STRAIGHT_JOIN의 힌트화)
  - **JOIN_ORDER**: 가능하다면 힌트에 지정된 순서대로 조인하도록 지시
  - **JOIN_PREFIX**: 가장 먼저 조인을 시작 할 테이블 지정
  - **JOIN_SUFFIX**: 가장 마지막으로 조인 할 테이블 지정
지정한 TABLE_NAME의 모든 테이블에 힌트가 적용되며, TABLE_NAME은 스키마 이름으로 한정할 수 없다.
TABLE_NAME에 별칭이 있는 경우 힌트는 테이블 이름이 아니라 별칭을 참조해야 한다.

**예시**
```sql
SELECT
/*+ JOIN_PREFIX(t2, t5@subq2, t4@subq1)
    JOIN_ORDER(t4@subq1, t3)
    JOIN_SUFFIX(t1) */
COUNT(*) 
FROM t1 JOIN t2 JOIN t3
WHERE t1.f1 IN (SELECT /*+ QB_NAME(subq1) */ f1 FROM t4)
  AND t2.f1 IN (SELECT /*+ QB_NAME(subq2) */ f1 FROM t5);
(SELECT /*+ QB_NAME(subq1) */ f1 FROM t4) : 쿼리 블록의 이름을 subq1로 지정
t4@subq1 : 쿼리 블록 subq1의 테이블 t4를 지정

--t2, t5@subq2, t4@subq1, t3, t1 순서대로 조인
/*+ JOIN_PREFIX(t2, t5@subq2, t4@subq1)
JOIN_ORDER(t4@subq1, t3)
 JOIN_SUFFIX(t1) */
```
 
 ## 4. 콜레이션(COLLATION)
- 데이터베이스에서 검색이나 정렬과 같은 작업을 할 때 사용하는 비교를 위한 규칙의 집합
- CHAR, VARCHAR, TEXT 등의 문자열 Datatype에는 문자셋(Character set)과 콜레이션(Collation) 속성이 있다.
- 문자셋(Character set)은 각 문자가 컴퓨터에 저장될 때 어떠한 '코드'로 저장될지에 대한 규칙의 집합을 의미하며 콜레이션(Collation)은 특정 문자 셋에 의해 데이터베이스에 저장된 값들을 비교 검색하거나 정렬 등의 작업을 위해 문자들을 서로 '비교' 할때 사용하는 규칙들의 집합을 의미한다.
- 예를 들어, 문자를 비교하는 경우, 'A'와 'a'중 무엇이 더 클지에 대한 비교 결과값은 설정된 콜레이션에 따라 달라진다. 
- utf8_bin 콜레이션을 적용하면 'A', 'B', 'a', 'b' 순으로 크고, utf8_general_ci는 'A', 'a', 'B', 'b' 순으로 크다.
- MySQL의 경우 utf8-general-ci 가 Default Collation이다.
![17](https://user-images.githubusercontent.com/48278519/149136203-daf7eb28-65b2-45d0-b7e1-1ac1e7083d3b.png)


## 5. 통계 정보

### 정의 
- **데이터베이스 마다 고유하게 존재하는 쿼리의 최적화와 실행계획을 작성하는 비용기반의 옵티마이저(Optimizer)가 참조하는 정보**
- 쿼리의 실행 계획을 최적화하기 위해서는 현재 데이터 저장 상태를 확인할 정보가 필요하며, 이러한 수집된 정보를(통계정보) 토대로 옵티마이저는 최적의 실행계획을 세운다. 
- RDS나 최근 버전의 옵티마이저는 대부분 비용기반(CBO)를 사용을 하기 때문에 통계정보도 매우 중요한 DB 관리요소이다.
- 통계정보가 정확하지 않는다면 전혀 다른 방향으로 쿼리가 실행될 수 있기 때문이다.
(ex, 1억 건의 레코드가 저장된 테이블의 통계 정보가 갱신되지 않아 레코드가 10건 미만인 것처럼 돼 있다면 옵티마이저는 인덱스 레인지 스캔(Index Range Scan)이 아니라 테이블을 처음부터 끝까지 읽는 풀테이블 스캔(Full Table Scan)으로 실행해 버릴 수도 있을 것이다. 이런 부정확한 통계 정보 탓에 매우 빠르게 끝나는 쿼리가 느리게 소요될 수도 있다.)

### 테이블 통계 정보

- **MySQL 5.5 이전 버전** : 각 테이블의 통계 정보가 메모리에서만 관리 되었고, SHOW INDEX 명령어로만 테이블의 인덱스 컬럼의 분포도를 확인 가능 -> 통계 정보가 메모리에서 관리될 경우, 서버가 재시작 되면 지금까지 수집된 통계 정보가 사라지기 때문에 재수집이 필요
- **MySQL 5.6 이후 버전** : 각 테이블의 통계정보를 mysql 데이터베이스(스키마) 의 innodb_index_stats 와 innodb_table_stats 테이블로 관리 할 수 있게 개선 -> InnoDB 스토리지 엔진을 사용하는 테이블에 대한 통계 정보를 영구적으로 관리
- **옵션값 설정** : 테이블을 생성 시  STATS_PERSISTENT 옵션을 설정할 수 있다.

```sql
mysql> CREATE TABLE tb_test(
id int,
col varchar(100),
PRIMARY KEY(id))
ENGINE=InnoDB
STATS_PERSISTENT={ DEFAULT | 0 | 1 }
```

- **STATS_PERSISTENT=0** :
 테이블의 통계 정보를 mysql 데이터베이스의 innodb_index_stats 와 innodb_table_stats 에 저장 X

- **STATS_PERSISTENT=1** : 
테이블의 통계 정보를 mysql 데이터베이스의 innodb_index_stats 와 innodb_table_stats 에 저장

- **STATS_PERSISTENT=DEFAULT** :
-  테이블을 생성할 때 STATS_PERSISTENT 구문을 생략하게 되면 선택되는 값. innodb_stats_persistent 설정 값을 따라가게 되고 해당 시스템 변수의 기본값은 ON 

**예시:**

```sql
select * from mysql.innodb_table_stats;
```
 - **database_name** : 데이터베이스 네임
 - **table_name** : 테이블 이름 ,파티션 이름 또는 서브 파티션
 - **last_update** : 이 행을 마지막으로 업데이트한 시간을 나타내는 시간
 - **n_rows** : 테이블의 전체 레코드 수
 - **clustered_index_size** : Primary Key 의 크기(InnoDB 페이지 개수). 
 - **sum_of_other_index_sizes** : PK를 제외한 인덱스의 크기(InnoDB 페이지 개수)
 *(Note) innodb_table_stats.sum_of_other_index_sizes 값은 테이블의 STATS_AUTO_RECAL 옵션에 따라 0으로 보일 수 있다. 이럴 경우 ANALYZE TABLE 명령어를 사용하면 통계값이 반영된다.
 

### 인덱스 통계 정보
- mysql.innodb_index_stats 에 저장되는 인덱스에 관한 통계 정보

```sql
select * from mysql.innodb_index_stats;
```

**stat_name** : stat_value열에 값이 보고되는 통계 이름 
  - 'n_diff_pfx%' : 인덱스가 가진 유니크한 값의 개수
  - 'n_leaf_page' : 인덱스 리프 노드의 페이지 수
  - size : 인덱스 트리에 전체 페이지수
(primary eky인덱스의 경우, 두 개의 n_diff%행이 있고, 행 수는 인덱스의 열 수와 같다. 고유하지 않은 인덱스의 경우 InnoDB는 기본키 열을 추가한다)

**stat_value** :  stat_name열에 이름이 지정된 통계 값 

**sample_size** : start_value 열에 제공된 견적에 대해 샘플링 된 페이지 수

**stat_description** : stat_name 열에 명명 된 통계 설명

**예시2 ) 인덱스 크기 검색**

```sql
-- 인덱스 크기 검색 
mysql > SELECT SUM(stat_value) pages, index_name, SUM(stat_value)* @@innodb_page_size size
		FROM nysql.innodb_index_stats WHERE table_name = 't1'
        AND stat_name = 'size' GROUP BY index_name;
```

<br>

# 5-2. 통계 정보 갱신 및 수집

## 자동 갱신
 - 테이블이 새로 오픈되는 경우
 -  테이블의 레코드가 대량으로 변경되는 경우(테이블의 전체 레코드 중에서 1/6 정도의 DML이 실행되는 경우)
 -  ANALYZE TABLE 명령어가 실행 되는 경우
 -  SHOW TABLE STATUS 명령이나 SHOW INDEX FROM 명령이 실행되는 경우
 -  InnoDB 모니터가 활성화된 경우
 -  innodb_stats_on_metadata 설정이 ON 인상태에서 SHOW TALBE STATUS 명령이 실행되는 경우
 
위의 경우 통계정보는 자동갱신이 된다. 테이블의 통계정보가 갱신이 되게 되면 즉 플랜이 의도치 않게 변경이 될 수도 있다. 이 때 변수값을 변경하여 통계 정보가 자동으로 갱신되는 것을 막을 수 있다.


- **STATS_AUTO_RECALC=1** :
테이블의 통계정보를 자동 수집 하며 테이블의 데이터가 약 10% 가 변경 될 때 통계가 다시 수집

- **STATS_AUTO_RECALC=0** :
테이블의 통계정보를 자동 수집을 비활성화

- **STATS_AUTO_RECALC=DEFAULT** :
테이블의 통계정보를 자동 수집 여부를 innodb_stats_auto_recalc 시스템 변수의 값으로 결정( innodb_stats_auto_recalc 시스템 변수의 기본값은 ON)
           

## 통계 수집
- 통계 정보를 수집 할 때 몇개의 InnoDB 테이블 블록을 샘플링 할지를 결정하는 옵션

### innodb_stats_transient_sample_pages
- 자동으로 통계 정보 수집이 실행될 때 '변수'개의 페이지만 임의로 샘플링 및 분석하여 결과를 통계정보로 활용하겠다는 의미 (기본값 : 8) 
-  innodb_stats_persistent 시스템 변수가 비활성화 된 경우 적용

### innodb_stats_persistent_sample_pages
- ANALYZE TABLE 명령이 실행되면 임의로 '변수'개 페이지만 샘플링 및 분석하여 결과를 영구적인 통계 정보 테이블에 저장 및 활용하겠다는 의미 (기본값 : 20)
- innodb_stats_persistent 시스템 변수가 활성화 된 경우 적용
- 더 정확한 통계 정보를 수집하고자 한다면 시스템 변수에 높은 값을 설정하면 조금 더 정확한 통계 정보를 수집할 수는 있지만, 통계를 수집 할때 I/O 증가 와 통계 정보 수집 시간이 길어질 수 있으므로 변수값은 적절한 설정이 필요하다.


### information_schema_stats_expiry
-  MySQL 8.0이후 추가된 변수 
-  information_schema_stats_expiry 변수에 지정된 시간 제한만큼 캐시를 TABLES 에 저장

```sql
예시1 ) MySQL 5.7

mysql> select count(*) from tb_test;
+----------+
| count(*) |
+----------+
| 10000000 |
+----------+

-- information_schema.TABLES 에서 통계정보가 확인
mysql> select table_name,table_rows,avg_row_length
       from information_schema.TABLES
       where table_name='tb_test';
+------------+------------+----------------+
| table_name | table_rows | avg_row_length |
+------------+------------+----------------+
| tb_test    |   9609638  |       233      |
+------------+------------+----------------+


mysql> select table_name,n_rows,clustered_index_size,
       sum_of_other_index_sizes
       from mysql.innodb_table_stats
       where table_name='tb_test';
+------------+---------+----------------------+--------------------------+
| table_name | n_rows  | clustered_index_size | sum_of_other_index_sizes |
+------------+---------+----------------------+--------------------------+
| tb_test    | 9609638 |         137152       |           14102          |
+------------+---------+----------------------+--------------------------+


mysql> select table_name,index_name,stat_name,stat_value
     from mysql.innodb_index_stats
     where table_name='tb_test';
+------------+------------+--------------+------------+
| table_name | index_name | stat_name    | stat_value |
+------------+------------+--------------+------------+
| tb_test    | PRIMARY    | n_diff_pfx01 |    9609638 |
| tb_test    | PRIMARY    | n_leaf_pages |     136987 |
| tb_test    | PRIMARY    | size         |     137152 |
| tb_test    | idx_1      | n_diff_pfx01 |    5787319 |
| tb_test    | idx_1      | n_diff_pfx02 |    9942726 |
| tb_test    | idx_1      | n_leaf_pages |      12247 |
| tb_test    | idx_1      | size         |      14102 |
+------------+------------+--------------+------------+


-- 테이블 truncate
mysql> truncate table tb_test;


-- 다시 information_schema.TABLES 를 조회 하면 truncate 되어서 0 으로 확인
mysql> select table_name,table_rows,avg_row_length
       from information_schema.TABLES
       where table_name='tb_test';
+------------+------------+----------------+
| table_name | table_rows | avg_row_length |
+------------+------------+----------------+
| tb_test    |      0     |        0       |
+------------+------------+----------------+
```

```sql
예시2 ) MySQL 8.0

-- MySQL 8.0
mysql> select count(*) from tb_test;
+----------+
| count(*) |
+----------+
| 3082751  |
+----------+


mysql> select table_name,table_rows,avg_row_length
       from information_schema.TABLES
       where table_name='tb_test';
+------------+------------+----------------+
| TABLE_NAME | TABLE_ROWS | AVG_ROW_LENGTH |
+------------+------------+----------------+
| tb_test    |  2971870   |      215       |
+------------+------------+----------------+


mysql> select table_name,n_rows,clustered_index_size,
       sum_of_other_index_sizes
       from mysql.innodb_table_stats
       where table_name='tb_test';
+------------+---------+----------------------+--------------------------+
| table_name | n_rows  | clustered_index_size | sum_of_other_index_sizes |
+------------+---------+----------------------+--------------------------+
| tb_test    | 2737323 |        39168         |           0              |
+------------+---------+----------------------+--------------------------+


mysql> select table_name,index_name,stat_name,stat_value
     from mysql.innodb_index_stats
     where table_name='tb_test';
+------------+------------+--------------+------------+
| table_name | index_name | stat_name    | stat_value |
+------------+------------+--------------+------------+
| tb_test    | PRIMARY    | n_diff_pfx01 |    2737323 |
| tb_test    | PRIMARY    | n_leaf_pages |      39021 |
| tb_test    | PRIMARY    | size         |      39168 |
+------------+------------+--------------+------------+


-- truncate table 실행
mysql> truncate table tb_test;


-- information_schema.tables 을 다시 조회
-- truncate 를 하였으나 여전히 건수가 확인됨
mysql> select table_name,table_rows,avg_row_length
       from information_schema.tables
       where table_name='tb_test';
+------------+------------+----------------+
| TABLE_NAME | TABLE_ROWS | AVG_ROW_LENGTH |
+------------+------------+----------------+
| tb_test    |    2971870 |            215 |
+------------+------------+----------------+


-- mysql.innodb_table_stats 통계 딕셔너리 테이블은 정상 반영
mysql> select table_name,n_rows,clustered_index_size,
       sum_of_other_index_sizes
       from mysql.innodb_table_stats
       where table_name='tb_test';  
+------------+--------+----------------------+--------------------------+
| table_name | n_rows | clustered_index_size | sum_of_other_index_sizes |
+------------+--------+----------------------+--------------------------+
| tb_test    |   0    |           1          |            0             |
+------------+--------+----------------------+--------------------------+
```
 
- 기본적으로 MySQL은 mysql.index_stats 및 mysql.table_stats 딕셔너리의 컬럼이 조회 될때 캐시된 값을 검색한다.(스토리지 엔진에서 직접 통계를 검색하는 것보다 더 효율적이기 때문)

- 만약 캐시된 통계를 사용할 수 없거나 만료된 경우 MySQL은 스토리지 엔진에서 최신 통계를 검색하여 mysql.index_stats 및 mysql.table_stats 사전 테이블에 캐시하며 캐시된 통계가 만료될 때까지 해당 값으로 검색한다.

- 위의 경우도 캐시에 저장된 값으로 검색이 되므로 truncate가 반영되지 않은 것처럼 나오는 것이다.

- 이 때, information_schema_stats_expiry는 캐시 통계가 만료되는 시간 제한값을 의미하며 기본값은 86400초(24시간)이지만 최대 1년까지 기간을 연장할 수 있다.

- 항상 스토리지 엔진에서 직접 최신 통계를 검색하고 캐시된 값을 무시하려면 information_schema_stats_expiry 시스템변수를 0 으로 설정하면 된다.

 
### 출처

- 업무에 바로 쓰는 SQL튜닝
- https://velog.io/@bae_mung/TIL-MySQL-Hint
- https://hwannny.tistory.com/101
- https://velog.io/@jooh95/DB-Scan-%EC%A2%85%EB%A5%98-%EC%A0%95%EB%A6%AC
- https://hoing.io/archives/2195
- https://sqlmvp.tistory.com/tag/%EC%98%B5%ED%8B%B0%EB%A7%88%EC%9D%B4%EC%A0%80
- https://myinfrabox.tistory.com/54

 
