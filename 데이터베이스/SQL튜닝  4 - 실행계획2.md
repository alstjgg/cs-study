# 1. Extra 컬럼
- SQL문을 어떻게 수행할 것인지에 대한 추가 정보를 보여주는 항목
- 부가 정보들은 세미클론(;)으로 구분하여 여러 가지 정보를 나열할 수 있으며 30여 가지 항목으로 정리할 수 있다.
- MySQL에서는 extra에서 수행되는 정보가 모두 출력되지는 않으므로 참고하는 수준으로 해석하는 것이 좋다.


## # const row not found
- 쿼리의 실행 계획에서 const 접근 방식으로 테이블을 읽었지만 실제로 해당 테이블에 레코드가 1건도 존재하지 않았을 경우 


## # distinct 
- JOIN시 필요한 것만 JOIN했고 중복된 값 제거한 경우
```sql
EXPLAIN
SELECT DISTINCT d.dept_no
FROM departments d, dept_emp de WHERE de.dept_no=de.dept_no;
```

## # Full scan on NULL key
-  where 조건에 nullable 컬럼이 있는 경우, null을 만나면 풀 스캔을 하겠다고 경고하는 의미
- 만약 col1이 NOT NULL로 정의된 칼럼이라면 Extra 칼럼에 해당 의미는 표시되지 않을 것 
- 해당 코멘트가  실행 계획의 Extra 칼럼에 표시됐다고 하더라도, 만약 IN이나 NOT IN 연산자의 왼쪽에 있는 값이 실제로 NULL이 없다면 풀 테이블 스캔은 발생하지 않으므로 걱정하지 않아도 됨

```sql
SELECT * FROM test
WHERE col1 IN (SELECT col2 FROM test2);
-- col1이 null인 경우에 풀스캔 하겠다라고 뜬다.
 ```

## # Impossible HAVING 
- 쿼리에 사용된 HAVING 절의 조건을 만족하는 레코드가 없을 때 발생 
- 쿼리의 내용을 다시 점검 필요


## # Impossible WHERE
- WHERE 조건이 항상 FALSE가 될 수 밖에 없는 경우를 의미
- 실제 데이터를 읽어보지 않고도 테이블 구조상 불가능한 조건이라고 판단이 가능 할 때 
- 쿼리 내용 점검 필요
- 아래  쿼리에서 WHERE 조건절에 사용된 emp_no 칼럼이 NOT NULL이라면 emp_no IS NULL 조건은 항상 FALSE가 된다.
```sql
EXPLAIN
SELECT * FROM employees WHERE emp_no IS NULL;
```

## # Impossible WHERE noticed after reading const tables
- 쿼리를 실행해보기 전엔 WHERE 조건이 항상 false인지는 몰랐지만 실행 후 false임을 알게된 경우
- 쿼리 내용 점검 필요
- const 는 상수로 치환되기 때문에 옵티마이저가 쿼리 일부를 실행해보기에 이런 결과가 나올 수 있다.
- ex) 아래 예시 쿼리는 실제로 실행되지 않으면 emp_no=0인 레코드가 있는지 없는지 판단할 수 없다. 그런데 실행 계획만 확인했을 뿐인데, 옵티마이저는 사번이 0인 사원이 없다는 것까지 확인하였다.
   > 이를 토대로 MySQL이 실행 계획을 만드는 과정에서 쿼리의 일부분을 실행해 본다는 사실을 알 수 있다.
   > 또한 이 쿼리는 employees 테이블의 프라이머리 키를 동등 조건으로 비교하기 때문에 const 접근 방식을 사용할 것이다. ```const 접근 방식이 필요한 부분은 실행 계획 수립 단계에서 옵티마이저가 직접 쿼리의 일부를 실행```하고, 실행된 결과 값을 원본 쿼리의 상수로 대체한다.
```sql
EXPLAIN
SELECT * FROM employees WHERE emp_no=0;
 ```

## # No matching min/max row 
-  MIN(), MAX()와 같은 집합 함수가 있는 쿼리의 WHERE 조건절에 일치하는 레코드가 하나도 없는 경우 (결과도 NULL로 반환됨)
- 쿼리 내용 점검 필요
ex) dept_no가 NOT NULL컬럼인데 빈 문자열 레코드를 검색하는 경우 
```sql
EXPLAIN
SELECT MIN(dept_no), MAX(dept_no)
FROM dept_empp WHERE dept_no='';
 ```
- ```참고) Extra 칼럼에 출력되는 내용 중에서 "No matching ..."이나 "Impossible WHERE ..." 등의 메시지는 잘못 생각하면 쿼리 자체가 오류인 것처럼 오해하기 쉽다. 하지만 Extra 칼럼에 출력되는 내용은 단지 쿼리의 실행 계획을 산출하기 위한 기초 자료가 없음을 표현하는 것뿐이다. Extra 칼럼에 이러한 메시지가 표시된다고 해서 실제 쿼리 오류가 발생하는 것은 아니다.```

## # No matching row in const table
- 조인에 사용된 테이블에서 const 방식으로 접근할 때 일치하는 레코드가 없는 경우
- 쿼리 내용 점검 필요
- "Impossible WHERE ..."와 같은 종류로, 실행 계획을 만들기 위한 기초 자료가 없음을 의미
```sql
EXPLAIN
SELECT *
FROM dept_emp de,
(SELECT emp_no FROM employees WHERE emp_no=0) tb1
WHERE tb1.emp_no=de.emp_no AND de.dept_no='d005'
```
![image](https://user-images.githubusercontent.com/48278519/151136791-6a3cbe81-2ec5-4a25-978b-e06689ab3575.png)
 

## # No tables used 
- FROM 절이 없거나 "FROM DUAL" 형태의 쿼리인 경우 
- MySQL에서는 FRM 절이 없는 쿼리도 오류 없이 실행되지만 오라클에서는 쿼리에 반드시 참조하는 테이블이 있어야 하므로 FROM 절이 필요없는 경우에 대비해 상수 테이블로 DUAL이라는 테이블을 참조한다. 또한 MySQL에서는 오라클과의 호환을 위해 FROM절에 'DUAL'이라는 테이블을 명시적으로 사용할 수도 있다. MySQL 옵타마이저가 FROM 절에 DUAL이라는 이름이 사용되면 내부적으로 FROM 절이 없는 쿼리 문장과 같은 방식으로 처리된다.
```sql
EXPLAIN SELECT 1;
EXPLAIN SELECT 1 FROM dual;
```

## # Not exists
-  A 테이블에는 존재하지만 B 테이블에 존재하지 않는 값을 조회하는 경우 두 가지 방법을 쓴다.
    - 안티조인(Anti-JOIN) : NOT IN (subquery) 형태나 NOT EXISTS 연산자를 사용한 방법
    - 아우터조인(LEFT OUTER JOIN) : 레코드가 많을 때 안티조인보다 더 나은 방법

- **아우터 조인을 이용해 안티-조인을 수행하는 쿼리에서는 Extra 칼럼에 Not exists 메시지가 표시된다.**
> Not exists 메시지는 이 쿼리를 NOT EXISTS 형태의 쿼리로 변환해서 처리했음을 의미하는 것이 아니라 MySQL이 내부적으로 어떤 최적화를 했는데 그 최적화의 이름이 "Not exists"이다.
```sql
EXPLAIN
SELECT *
FROM dept_emp de
  LEFT JOIN departments d ON de.dept_no=d.dept.no
WHERE d.dept_no IS NULL;
 ```


## # Range checked for each record (index map: N)
- 두 테이블을 조인하는 경우 WHERE 조건에 변수가 두 개가 사용되어 계속해서 변수 값이 바뀐다. 즉 매 레코드마다 인덱스를 탈지 풀 스캔을 할지 결정해야하는 쿼리인 경우 발생
```sql
SELECT * FROM employees e1, employees e2
WHERE e1.emp_no >= e2.emp_no;
-- 이 경우 e1 테이블의 레코드를 읽고 e2의 레코드를 읽는데 e1 테이블 레코드를 읽을 때마다 값이 달라진다.
```
- 위 예시처럼 조인 조건에 둘 다 변수(e1.emp_no와 e2.emp_no)인 경우, MySQL 옵티마이저는 e1 테이블을 먼저 읽고 e2를 읽을 때 인덱스 레인지 스캔과 풀 테이블 스캔 중에서 어느 것이 효율적일지 판단할 수 없다.
- EX) 사번이 1번부터 1억까지 있다고 할 때 e1 테이블을 처음부터 끝까지 스캔하면서 e2 테이블에서 e2.emp_no >= e1.emp_no 조건을 만족하는 레코드를 찾음
    - **e1.emp_no=1인 경우** : e2 테이블의 1억건 전부를 읽어야 함 -> 풀 테이블 스캔으로 접근
    - **e1.emp_no=100000000인 경우** : e2 테이블을 한 건만 읽음 -> 인덱스 레인지 스캔으로 접근



## # Scanned N databases
- DB 메타정보(테이블,컬럼,인덱스등...)가 저장되어 있는 DB(INFORMATION_SCHEMA)를 조회하는 경우
- "Scanned N databases"에서 N은 몇 개의 DB 정보를 읽었는지 보여주는 것인데, N은 0가 1 또는 all의 값을 가짐
   - 0 : 특정 테이블의 정보만 요청되어 데이터베이스 전체의 메타 정보를 읽지 않음
   - 1 : 특정 데이터베이스 내의 모든 스키마 정보가 요청되어 해당 데이터베이스의 모든 스키마 정보를 읽음
   - All : MySQL 서버 내의 모든 스키마 정보를 다 읽음
 

## # Select tables optimized away
- MIN() 또는 MAX()만 SELECT에 사용되거나, MIN(), MAX()를 조회하는 쿼리가 적절한 인덱스를 사용하여 인덱스를 오름차순 또는 내림차순으로 1건만 읽는 형태의 최적화가 적용된 경우
- MyISAM 테이블에 대해서는 GROUP BY 없이 COUNT만 SELECT할 때도 이런 형태의 최적화가 적용(WHERE 절에 조건을 가질 때는 이러한 최적화를 사용하지 못한다.)

```sql
EXPLAIN;
SELECT MAX(emp_no), MIN(emp_no) FROM employees;

EXPLAIN
SELECT MAX(from_date), MIN(from_date) FROM salaries WHERE emp_no=10001;
```

## # unique row not found
- 두 개의 테이블이 프라이머리 키 또는 유니크 컬럼으로 아우터 조인을 수행하는 쿼리에서 **아우터 테이블에 일치하는 레코드가 없을 때**
- "A LEFT JOIN B"에서 A테이블도 B테이블도 유니크한테 A테이블에만 있는 레코드로 조인하려고 하면 B랑 조인하는 컬럼 값이 없는 경우가 발생할 것이므로 해당 멘트가 표시된다.


## # Using index(커버링 인덱스)
- 데이터 파일을 전혀 읽지 않고 **인덱스만 읽어서 쿼리를 처리할 수 있는 경우(커버링 인덱스를 탔을 때)**
- 인덱스를 이용해 처리하는 쿼리에서 가장 큰 부하를 차지하는 부분은 인덱스를 검색해 일치하는 레코드의 나머지 칼럼 값을 가져오기 위해 데이터 파일을 찾아서 가져오는 작업이다. 
- 하지만 select 문의 결과로 인덱스 컬럼만 필요한 경우 필요한 칼럼이 모두 인덱스에 있으므로 나머지 칼럼이 저장된 데이터 파일을 읽어올 필요가 없게 되고, **매우 빠른 속도로 처리**된다.
- 이렇게 인덱스만으로 처리되는 것을 ```커버링 인덱스(Covering index)```라고 한다. 인덱스 레인지 스캔을 사용하지만 쿼리의 성능이 만족스럽지 못한 경우라면 인덱스에 있는 칼럼만 사용하도록 쿼리를 변경해 큰 성능 향상을 볼 수 있다.

```sql
SELECT first_name
FROM employees WHERE first_name BETWEEN 'Babette' AND 'Gad';
id	select_type	table	type	key	key_len	ref	rows	Extra
1	SIMPLE	employees	range	ix_firstname	42	 	...	Using index
```

 
## # Using index for group-by
- GROUP BY 처리를 위해 (이미 정렬된) 인덱스를 이용하는 경우 (루스 인덱스 스캔을 사용하는 경우)
- GROUP BY 처리를 위해 MySQL 서버는  GROUPING 기준 칼럼을 이용해 정렬 작업을 수행하고 다시 정렬된 결과를 그룹핑하는 형태의 고부하 작업을 필요로 한다. 하지만 GROUP BY 처리가 인덱스(B-Tree 인덱스)를 이용하면 정렬된 인덱스 칼럼을 순서대로 읽으면서 그룹핑 작업만 수행하므로 효율적이다.
- GROUP BY 처리를 위해 인덱스를 읽는 방법을 ```루스 인덱스 스캔```이라고 한다.
- GROUP BY 처리를 위해 단순히 인덱스를 순서대로 쭉 읽는 타이트 인덱스 스캔과는 달리 루스 인덱스 스캔은 인덱스에서 필요한 부분만 듬성 듬성 읽는다.
  - **타이트 인덱스 스캔(인덱스 스캔)을 통한 GROUP BY 처리**
   - AVG()나 SUM() 또는 COUNT()와 같이 조회하려는 값이 모든 인덱스를 다 읽어야 할 때 사용
   - 이런 쿼리의 런 쿼리의 실행 계획에는 "Using index for group-by" 메시지가 출력되지 않는다.

  - **루스 인덱스 스캔을 통한 GROUP BY 처리**
   - MIN()이나 MAX()와 같이 조회하는 값이 인덱스의 첫 번째 또는 마지막 레코드만 읽어도 되는 쿼리에 사용


# Using join buffer
- 일반적으로 조인에 사용되는 컬럼은 인덱스를 생성하며, 조인하기 위해 먼저 읽은 테이블을 드라이빙(Driving)테이블이라 하고 나중에 읽는 테이블을 드리븐(Driven) 테이블이라 한다.
- 만약 드리븐 테이블에 JOIN되는 컬럼에 인덱스가 없으면 MySQL 서버는 조인 버퍼(JOIN BUFFER)라는 임시 공간을 사용해서 JOIN한다. 이 때 **조인 버퍼가 사용되는 실행 계획의 Extra 칼럼에는 "Using join buffer"라는 메시지가 표시**된다.
- 참고로,조인 버퍼는 join_buffer_size라는 시스템 설정 변수에 최대 사용 가능한 버퍼 크기를 설정할 수 있다
```sql
--from_date에 인덱스 없음
EXPLAIN
SELECT *
FROM dept_emp de, employees e
WHERE de.from_date > '2005-01-01' AND e.emp_no < 10994;
```

 
# Using sort_union, Using union, Using intersect 
- 쿼리는 "index_merge" type의 접근 방법을 사용할 때만 2개 이상의 인덱스가 사용될 수 있는데,  두 인덱스로부터 읽은 결과를 어떻게 합쳤는지를 보여줄 때 나타난다.
  - **Using intersect** : 각각 인덱스를 사용하는 조건이 AND로 연결된 경우, 처리 결과에서 교집합을 추출했다는 의미
  - **Using union** : 각각 인덱스를 사용하는 조건이 OR로 연결된 경우, 처리 결과에서 합집합을 추출했다는 의미
  - **Using sort_union** : Using union과 같은 작업을 수행하지만 Using union으로 처리될 수 없는 경우
 

## # Using temporary
- MySQL이 쿼리를 처리하는 동안 중간 결과를 담아 두기 위해 임시 테이블을 사용한 경우
- 사용된 임시 테이블이 메모리에 생성됐었는지 디스크에 생성됐었는지는 실행 계획만으로 판단할 수 없다.
- 다음의 경우 임시테이블을 생성한다.
  - FROM 절에 서브쿼리를 쓰면 무조건 임시테이블(Derived table)이 생성
  - "COUNT(DISTINCT column1)"를 포함하는 쿼리도 인덱스를 사용할 수 없는 경우 임시 테이블이 생성
  - UNION, UNION ALL이 사용된 쿼리도 임시 테이블을 생성
  - 인덱스를 사용하지 못하는 정렬 작업도 임시 테이블을 생성
- 아래 쿼리는 GROUP BY 칼럼과 ORDER BY 칼럼이 다르기 때문에 임시 테이블이 필요한 작업이다. 인덱스를 사용하지 못하는 GROUP BY 쿼리는 실행 계획에서 "Using temporary" 메시지가 표시되는 가장 대표적인 형태의 쿼리다.
```sq;
EXPLAIN
SELECT * FROM employees GROUP BY gender ORDER BY MIN(emp_no);
 ```
 
 
## # Using filesort
- ORDER BY를 처리하기 위해 인덱스를 이용할 수도 있지만 적절한 인덱스를 사용하지 못하는 경우 발생
- 예를 들어, GROUPING을 하는 과정에서 MySQL에서 정렬을 한 번 해야하는데 인덱스를 사용하지 못해서 소트 버퍼에 레코드를 복사해서 정렬하는 비효율적인 작업을 하는 것을 의미한다.
- 실무에서 정렬은 거의 필수라 그런지 제일 자주 보이는 비효율적인 케이스라고 볼 수 있다. (= 튜닝 대상이 된다.)


## # Using where
- MySQL엔진이 별도의 가공, 필터링 작업을 처리한 경우

```sql
SELECT * FROM employees WHERE emp_no BETWEEN 100 AND 110 AND gender = 'F';
-- 범위조건 BETWEEN은 스토리지 엔진에 의해 10개의 레코드가 리턴되고
-- 10개중에 체크 조건 gender='F' 인 것을 MySQL엔진에 의해 필터링되어 Using Where 등장
 ```

## # 쿼리가 요건을 제대로 반영하고 있는지 확인해야 하는 경우
- Full scan on NULL key
- Impossible HAVING
- Impossible WHERE
- Impossible WHERE noticed after reading const tables
- No matching min/max row
- No matching row in const table
- Unique row not found

 
# 2. Filtered 컬럼
- SQL문을 통해 db엔진으로 가져온 데이터를 대상으로, 필터 조건에 따라 어느 정도의 비율로 데이터를 제거했는지를 의미하는 항목
- ex) DB엔진으로 100건의 데이터를 가져왔다고 가정하면, 이후 where 절의 "사원번호 between 1 and 10"조건으로 100건의 데이터가 10건으로 필터링 되고, filtered에는 10이라는 정보가 출력된다.(단위는 %)
 
# 3. 좋고 나쁨을 판단하는 기준
 ![최근](https://user-images.githubusercontent.com/48278519/151145727-81445df4-4045-40b8-af92-506ec121ebe0.png)


# 4. 출처
- 업무에 바로 쓰는 SQL 튜닝
-https://jeong-pro.tistory.com/243
- https://velog.io/@jsj3282/28.-MySQL-%EC%8B%A4%ED%96%89-%EA%B3%84%ED%9A%8D-%EC%8B%A4%ED%96%89-%EA%B3%84%ED%9A%8D-%EB%B6%84%EC%84%9D6

