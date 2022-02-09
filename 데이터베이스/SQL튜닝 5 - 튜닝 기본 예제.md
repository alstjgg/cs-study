# 1. 실습 데이터 세팅
- 실습 데이터 URL : https://github.com/7ieon/SQLtune 


### (1) 사원 테이블
해당 기업에 소속된 직원들의 정보를 저장하는 테이블
|컬럼|의미|
|---|---|
|사원번호|사원이라는 오브젝트를 유일하게 구분할 수 있는 숫자
|생년월일| 해당 사원이 태어난 년/월/일에 대한 정보로, 날짜 타입으로 구성|
|이름|사원의 이름|
|성 as name| 사원의 가족 이름 정보|
|성별 gender| 사원의 성별 정보, M male (남성)과 Fermale (여성)이라는 데이터만 입력할 수 있음|
|입사일자|사원이 해당 기업에 입사한 날짜로, 날짜 타입으로 구성|

### (2) 부서 테이블 
조직에서 관리하는 업무 부서에 관한 정보를 저장하는 테이블
|컬럼|의미|
|---|---|
|부서번호| 부서를 유일하게 식별할 수 있는 문자열|
|부서명| 부서 이름|
|비고|해당 부서가 현재 유효한지를 나타내는 정보로 NULL 값이 포함될 수 있음|

### (3) 부서사원 매핑 테이블
부서 테이블과 사원 테이블 간의 매핑 정보. 즉, 한 명의 사원은 특정 부서에 소속되므로 그에 해당하는 연결 정보를 저장
|컬럼|의미|
|---|---|
|사원번호| 사원을 유일하게 식별할 수 있는 숫자|
|부서번호| 부서를 유일하게 식별할 수 있는 문자열|
|시작일자| 사원이 해당 부서에 소속된 일자|
|종료일자| 사원이 해당 부서에 소속 해지된 일자|

### (4) 부서관리자 테이블
부서를 대표하는 관리자 사원의 정보가 저장되는 테이블
|컬럼|의미|
|---|---|
|사원번호| 관리자에 해당되는 사원번호|
|부서번호| 부서를 유일하게 식별할 수 있는 문자열|
|시작일자| 해당 부서에 사원이 관리자 역할로 임명된 일자|
|종료일자| 해당 부서에 사원이 관리자 역할에서 해지된 일자|

### (5 )직급 테이블
사원이 위치한 포지션을 나타내는 테이블로, 과거의 직급 정보부터 현재의 직급 정보까지 적재
|컬럼|의미|
|---|---|
|사원번호| 사원을 유일하게 식별할 수 있는 숫자|
|직급명| 사원이 위치한 포지션|
|시작일자| 해당 직급이 부여된 일자|
|종료일자| 해당 직급이 만료된 일자로, NULL일 때는 종료일자가 정해지지 않았음을 의미|


### (6) 급여 테이블
사원별로 매년 계약한 연봉 정보가 저장되는 테이블로서, 과거 급여부터 현재 급여까지 매번 적재
|컬럼|의미|
|---|---|
|사원번호|사원을 유일하게 식별하는 숫자|
|연봉| 계약된 연봉 숫자|
|시작일자| 해당 연봉 정보가 유효한 시작 일자|
|종료일자| 해당 연봉 정보가 만료되는 일자|
|사용여부| 해당 연봉정보의 활성화 여부를 나타내는 문자로서 NULL 값이 포함될 수 있음|


### (7) 사원출입기록 테이블
지역별, 출입문별로 출입한 이력에 관한 시간 정보를 적재하는 테이블
|컬럼|의미|
|---|---|
|순번|자동적으로 숫자가 증가하는 AUTO_INCREMENT로 설정된 숫자(시원스)|
|사원번호| 사원을 유일하게 식별할 수 있는 숫자|
|입출입시간| 출입한 시간정보로 TIMESTAMP 형식으로 적재됨|
|입출입구분| 출입의 구분자를 I 와 O 이라는 문자로 저장하는 열|
|출입문| 출입문 코드로 NULL이 포함될 수 있음|
|지역| 지역 코드로 NULL이 포함될 수 있음|


![4](https://user-images.githubusercontent.com/48278519/153126622-4c57179b-b260-4387-a677-4ab53c7b75eb.png)
![5](https://user-images.githubusercontent.com/48278519/153126629-2614748b-67f7-4506-8e73-38dbbe73b5ac.png)


<br>
<br>

# 2. SQL문 단순 수정으로 착한 쿼리 만들기
![6](https://user-images.githubusercontent.com/48278519/153126633-b67b1d7d-dacb-45ee-bc00-a5cf523a7b3f.png)
<br>

## 1) 사원번호가 1100으로 시작하면서 사원번호가 5자리인 사원 정보 출력
### # AS_IS 
```sql
-- 0.329 sec / 0.000 sec
SELECT *
FROM 사원
WHERE SUBSTRING(사원번호,1,4) = 1100
	AND LENGTH(사원번호) = 5
```
![7](https://user-images.githubusercontent.com/48278519/153126984-a30aa2b7-46ed-42ce-9c77-163aacb10d3d.png)


### # Tunning
- 기본키인 사원번호 접근 시 WHERE 절에서 가공하여 접근
=> Table Full Scan이 발생 (type = 'ALL')


### # TO_BE
```sql
-- 0.000 sec / 0.000 sec
SELECT *
FROM 사원
WHERE 사원번호 BETWEEN 11000 AND 11009
```
![8](https://user-images.githubusercontent.com/48278519/153127021-dbf1303e-0d2b-4ae4-857e-c563957c4fa4.png)


### # Conculusion
- ```기본키를 변형하지 않도록 한다.```

<br>
<br>

## 2) 사원 테이블에서 성별 기준으로 몇 명의 사원이 있는지 출력하는 쿼리 
### # AS_IS
```sql
-- 1.187 sec / 0.000 sec
select IFNULL(성별,'NO DATA') AS 성별, COUNT(1) 건수
from 사원
group by IFNULL(성별, 'NO DATA')
```
![9](https://user-images.githubusercontent.com/48278519/153127422-d270cfbd-df44-4f84-97f7-fcf5b587cb83.png)
- key 항목이 "I_성별_성" INDEX로 나타나므로 INDEX FULL SCAN으로 수행되었으며, Extra 방식이 "Using temporary"이므로 임시테이블을 생성

### # Tunning 
- 사원 테이블의 성별에는 NULL값이 존재하지 않고 M / F 만 저장됨
- 사원 테이블의 구조를 보면 성별은 NOT NULL 조건이 있음
=> ```현재 IFNULL()함수를 처리하기 위해 DB 내부적으로 별도의 임시테이블을 만들면서 null여부를 검사하려 했지만, 이는 불필요한 함수이다.```


### # TO_BE
```sql
-- 0.250 sec / 0.000 sec
select 성별, COUNT(1) 건수
FROM 사원
GROUP BY 성별;
```
![10](https://user-images.githubusercontent.com/48278519/153127245-2778371e-1fc1-4387-803c-9b3697608cf6.png)
- 임시 테이블 없이 인덱스만 사용하여 데이터 추출


### # Conculusion
- ```쿼리 실행 시 불필요한 함수를 호출하지 않도록 한다.```

<br>
<br>

## 3) 급여 테이블에서 현재 유효한 급여 정보만 조회하고자, 사용여부 열의 값이 1인 데이터 출력하는 쿼리
### # AS_IS
```sql
-- 1.000 sec / 0.000 sec
select count(1)
from 급여
where 사용여부 = 1
```
![11](https://user-images.githubusercontent.com/48278519/153127773-d9904c5b-c2b3-455d-b2de-c08167cfdd76.png)
- key 항목이 I_사용여부 로 출력되므로 해당 인덱스를 사용
- type 항목이 index이므로 인덱스 풀 스캔으로 수행
- filtered 항목이 10.00이므로 MySQL 엔진으로 가져온 데이터 중 10%를 추출해서 최종 데이터를 출력함


### # Tunning
![12](https://user-images.githubusercontent.com/48278519/153127802-6da209ff-f4d5-4432-bd91-e574e1528233.png)
- 총 50만 건의 데이터가 있고, 사용 여부 열에는 0, 1, ' '데이터가 있음
- 사용여부 열 값이 '1'인 데이터는 전체 데이터 대비 10%이하

![13](https://user-images.githubusercontent.com/48278519/153127817-8898125d-812e-4751-9a0b-d7493c408ce8.png)
- 기본 키는 사원번호/시작일자 이며, I_사용여부 인덱스는 사용여부 열로 구성<br>
=> 사용여부 컬럼이 인덱스로 구성되었고, WHERE절의 조건문으로 작성되었음에도 실행계획에선 INDEX FULL SCAN으로 수행<br>
=> 조건문 파악 필요

![14](https://user-images.githubusercontent.com/48278519/153127868-acbc2b30-0dd4-4c70-86b2-811759e58b19.png)
- 사용 여부 컬럼은 문자형이 char(1)인 데이터 유형이지만, where 조건에선 숫자 유형으로 데이터에 접근<br>
=> DBMS 내부의 묵시적 형변환 발생 <br>
=> 전체 데이터를 스캔하는 FULL SCAN 발생 


### # TO_BE
```sql
-- 0.031 sec / 0.000 sec
select count(1)
from 급여
where 사용여부 = '1';
```
![15](https://user-images.githubusercontent.com/48278519/153127893-9cf5ad63-9427-4229-a3c4-2da19ac37e77.png)

- key 항목에서 I_사용여부 인덱스를 사용하여 데이터 접근
- 사용여부 = '1' 조건 절이 스토리지 엔진에 전달되어 필요한 데이터만 가져옴


### # Conculusion
- ```형 변환으로 인덱스를 활용하지 못하는 예를 없애도록 한다.```

<br>
<br>

## 4) 사원 테이블에서 남자인 Radwan사원을 출력하는 쿼리
### # AS_IS
```sql
-- 0.297 sec / 0.000 sec
select *
from 사원
where concat(성별,' ',성) = 'M Radwan';
```
![16](https://user-images.githubusercontent.com/48278519/153128107-a61b9edc-8292-4f93-b5fb-dcb95e203811.png)
- 사원 테이블은 where 조건절(concat 함수 사용으로 데이터 변형)로 데이터에 접근
- type 항목이 ALL로 테이블 풀 스캔으로 접근(rows : 약 30만개)


### # Tunning
![17](https://user-images.githubusercontent.com/48278519/153128124-07ec06b4-1e26-4192-b3b8-a421a6100ade.png)
- 인덱스인 I_성별_성 인덱스를 사용할 수 있고, 조건문도 동등조건이므로, 인덱스를 활용하여 조회가 가능한 쿼리


### # TO_BE
```sql
-- 0.031 sec / 0.000 sec
select *
from 사원
where 성별 = 'M' and 성 = 'Radwan'
```
![18](https://user-images.githubusercontent.com/48278519/153128349-72d9d425-12ba-4b5d-9bd2-c94b946f681c.png)
- 인덱스의 동등조건을 사용하였으므로 type 컬럼이 ref
- rows는 102 건


### # Conculusion
- ```열을 결합하여 사용하지 않도록 한다.(결합하려는 열이 인덱스가 존재하는 경우)```

<br>
<br>

## 5) 부서 관리자의 사원번호 ,이름, 성, 부서번호 데이터를 중복 제거하여 조회하는 쿼리
### # AS_IS
```sql
-- 0.000 sec / 0.000 sec
select distinct 사원.사원번호, 사원.이름, 사원.성, 부서관리자.부서번호
from 사원
join 부서관리자
	on (사원.사원번호 = 부서관리자.사원번호)
```
![19](https://user-images.githubusercontent.com/48278519/153128554-b53e077c-9f4e-4e4d-8a9b-2929efe468ca.png)
- 드라이빙 테이블 : 부서관리자 / 드리븐 테이블 : 사원 
- 부서 관리자 테이블의 type : index ( index full scan 방식으로 수행됨)
- 사원 테이블의 type : eq_ref (사원번호라는 primary key를 사용하여 1건의 데이터를 조회하는 방식으로 조인됨)
- Extra : Using temporary (distinct를 수행하고자 별도의 임시테이블 생성)


### # Tunning
- 사원 테이블의 primary key는 '사원번호'이므로, select 절의 사원.사원번호 에는 중복 데이터가 존재하지 않음<br>
=> ```distinct 키워드로 정렬작업 후 중복 제거하는 작업은 불필요함```<br>
(참고 : distinct 키워드는 나열될 열들을 정렬한 뒤 중복 데이터는 삭제한다. 따라서 distinct를 쿼리에 작성하는 것만으로도 정렬 작업이 포함됨을 인지해야 함)


### # TO_BE
```sql
-- 0.000 sec / 0.000 sec
select 사원.사원번호, 사원.이름, 사원.성, 부서관리자.부서번호
from 사원
join 부서관리자
	on (사원.사원번호 = 부서관리자.사원번호)
```
![20](https://user-images.githubusercontent.com/48278519/153128622-92edb47f-622d-41a6-a962-0e058f3383b4.png)
- Extra 항목의 Using temporary가 삭제됨(불필요한 작업 제거)


### # Conculusion
- ```불필요한 distinct는 하지 않아야 한다.```

<br>
<br>

## 6) 성이 Baba인 사원 데이터를 남/ 녀 별로 나누어 union으로 조회하는 쿼리
### # AS_IS
```sql
-- 0.000 sec / 0.000 sec
select 'M' as 성별, 사원번호
from 사원
where 성별 = 'M'
  and 성 = 'Baba'
union
select 'F', 사원번호
from 사원
where 성별 = 'F'
  and 성 = 'Baba'
```
![21](https://user-images.githubusercontent.com/48278519/153128889-d28af772-5878-4889-a2bf-c1025ab8d6ef.png)
- id가 null인 행에서 id = 1인 행과 id = 2인 행의 결과를 통합하여 중복제거 하는 union 작업 수행
- 이 때, 임시 테이블을 생성하게 됨. 만약 메모리에 상주하기 어려울 만큼 id = 1,2의 결과량이 많다면 메모리가 아닌 임시 파일을 생성할 수 도 있다.


### # Tunning
![22](https://user-images.githubusercontent.com/48278519/153128894-8234337a-48c6-4b52-afef-4dd0a4eeb969.png)
- where 절의 성별 / 성 컬럼이 동등 조건으로 작성되어 있으므로, I_성별_성 index로 빠르게 조회 가능
- 또한 union 연산으로 통합하는 과정에서 두 결과를 합친 후 중복제거를 하지만 이미 사원번호라는 기본키가 출력되는 SQL문에서 중복제거는 불필요


### # TO_BE
```sql
-- 0.000 sec / 0.000 sec
select 'M' as 성별, 사원번호
from 사원
where 성별 = 'M'
  and 성 = 'Baba'
union all
select 'F', 사원번호
from 사원
where 성별 = 'F'
  and 성 = 'Baba'
```
![23](https://user-images.githubusercontent.com/48278519/153129052-38aed109-f8a3-4e3f-8556-848f65e7f27e.png)
- 정렬 및 중복 제거 작업이 없어짐 (AS_IS의 3번째 행이 없어짐)


### # Conculusion
- ```불필요한 union(중복제거)처리는 하지 않는다.```

<br>
<br>

## 7) 성과 성별로 grouping하는 쿼리
### # AS_IS
```sql
-- 0.016 sec / 0.062 sec
select 성, 성별, count(1) as 카운트
from 사원
group by 성, 성별
```
![24](https://user-images.githubusercontent.com/48278519/153129243-cf2b8d0c-ef5e-4df8-a62b-40daf96e42cc.png)
- I_성별_성 index를 사용하고, 임시테이블을 생성하여 성 / 성별을 grouping 해 count() 연산 수행
- I_성별_성 index의 구성 컬럼이 group by 절에 포함되므로, 테이블 접근 없이 인덱스만 사용하는 커버링 인덱스(Using Index)로 수행


### # Tunning
- 인덱스를 활용하는데도 메모리나 디스크에 임시테이블을 꼭 생성해야 하는지에 대한 고민이 필요 (인덱스만으로 count연산을 수행할 수 있다면 임시테이블은 필요가 없을 것)<br>
=> I_성별_성 index는 성별 컬럼 기준으로 정렬 후 성 컬럼으로 정렬되었다는 의미이므로, 인덱스 순서 활용 가능


### # TO_BE
```sql
-- 0.109 sec / 0.219 sec
-- 인덱스 순서대로 grouping
select 성, 성별, count(1) as 카운트
from 사원
group by 성별, 성
```
![25](https://user-images.githubusercontent.com/48278519/153129314-38826b2b-febf-4ee2-9031-39ce540be475.png)


### # Conculusion
- ```인덱스의 열을 고려하여 정렬 작업을 수행해야 한다.```

<br>
<br>

## 8) 입사일자가 '1989'로 시작하면서 사원번호가 100,000을 초과하는 데이터를 조회하는 쿼리
### # AS_IS
```sql
-- 0.031 sec / 0.265 sec
select 사원번호
from 사원
where 입사일자 LIKE '1989%'
  and 사원번호 > 100000
```
![26](https://user-images.githubusercontent.com/48278519/153129637-16994eab-15dd-4268-b775-6b7becf323d8.png)
- 사원 번호인 primary key로 index range scan 실행
- 스토리지 엔진으로부터 기본 키를 구성하는 사원번호를 조건으로 데이터를 가져온 뒤, MySQL 엔진에서 남은 필터 조건(입사일자 like '1989%')으로 추출하기 때문에 filtered가 11.11%가 나옴


### # Tunning
![27](https://user-images.githubusercontent.com/48278519/153129653-8dcdf5df-ec9e-43e0-a63c-50c2d62fc960.png)
- 전체 데이터 개수는 약 30만개
- 입사일자 like '1918%'을 만족하는 데이터 개수는 28394개
- 사원번호 > 100000 인 데이터 개수는 약 20만개 <br>
=> 사원번호가 100000을 초과하는 데이터의 비율이 전체 데이터의 70%를 차지하므로, 스토리지 엔진에서 데이터 접근 시 기본키(사원번호)로 접근하는 것이 효율적이지 않을 수 있음<br>
=> 한편, 입사일자가 1989%인 사원 수는 전체 데이터 대비 10%이므로, 입사일자 열을 데이터 엑세스 조건으로 활용 검토


### # TO_BE
```sql
-- 0.016 sec / 0.062 sec
select 사원번호
from 사원  
where 입사일자 >= '1989-01-01'and 입사일자 < '1990-01-01' 
 and 사원번호 > 100000
```
![28](https://user-images.githubusercontent.com/48278519/153129853-54ee7fff-a443-40b0-a73d-10c187438867.png)
-입사일자 컬럼의 데이터 유형이 date이고, LIKE 절보다 부등호(< > <= >=) 절이 우선하여 인덱스를 사용하므로, 데이터 접근 범위를 줄임
- I_입사일자 index를 활용하여 index range scan을 수행
- 테이블에 직접 접근하지 않고 I_입사일자 index만 사용하여 최종 결과를 출력 (커버링 인덱스 스캔, extra 항목의 Using index라고 함)


### # Conculusion
- ```더 효율적인 인덱스를 사용할 수 있는지에 대한 검토가 필요하다.```

<br>
<br>

## 9) B출입문으로 출입한 이력이 있는 정보를 조회하는 쿼리
### # AS_IS
```sql
-- 0.016 sec / 1.875 sec
select *
from 사원출입기록
where 출입문 = 'B';
```
![29](https://user-images.githubusercontent.com/48278519/153130005-76a020f9-ef79-461c-9fd4-b814db4cac61.png)


### # Tunning
![30](https://user-images.githubusercontent.com/48278519/153130036-42c9f4f8-ad76-4b26-89f4-b9b36da972b8.png)
- 사원출입기록 테이블 중 출입문 B는 총 66만건 데이터 중 30만건을 차지하므로 전체 데이터의 50%<br>
=> 앞의 실행계획에 따르면 I_출입문 index로 index scan을 수행하는데, ```전체 데이터의 약 50%에 달하는 데이터를 조회하기 위해 인덱스를 활용하는 것은 효율적이지 않을 수 있다.```


### # TO_BE
```sql
-- 0.297 sec / 0.656 sec
select *
from 사원출입기록 ignore index(I_출입문)
where 출입문 = 'B';
```
![31](https://user-images.githubusercontent.com/48278519/153130098-0598fdd3-e348-4b1e-a702-9961701ad639.png)
- 인덱스를 무시할 수 있도록 IGNORE INDEX라는 힌트 사용
- 랜덤 엑세스가 발생하지 않고, 한번에 다수의 페이지에 접근하는 테이블 풀 스캔 방식으로 수행되므로 더 효율적으로 실행


### # Conculusion
- ```전체 데이터 대비 조회 결과 데이터 비율이 높다면, 인덱스를 활용하지 않는 편이 좋을 수 있다.```


<br>
<br>

## 10) 입사일자가 1994년 1월1일부터 2000년 12월 31일까지인 사원들의 이름과 성을 출력하는 쿼리
### # AS_IS
```sql
-- 0.141 sec / 1.188 sec
select 이름, 성
from 사원
where 입사일자 between STR_TO_DATE ('1994-01-01', '%Y-%m-%d')
				  and STR_TO_DATE ('2000-12-31', '%Y-%m-%d')
```
![32](https://user-images.githubusercontent.com/48278519/153130232-b9941e3d-fc3c-4619-aa13-64693c7f6b49.png)

- 사원 테이블에서 I_입사일자 index로 range scan 수행
- Extra의 Using index condition : 스토리지 엔진에서 입사일자 조건절로 인덱스 스캔을 수행
- Extra의 Using MRR : 인덱스가 랜덤 액세스가 아닌 순차 스캔으로 처리


## # Tunning
- 사원 테이블 데이터는 총 30만건인데 결과값은 4만건으로, 인덱스 스캔으로 랜덤 엑세스 부하 발생
- 입사일자 열 기준으로 매 번 1994~2000년의 데이터를 조회하는 경우가 잦다면, 랜덤 액세스의 부하가 발생하도록 하기보단 테이블 풀 스캔 방식으로 설정하는 것이 효율적일 수 있음


### # TO_BE
```sql
-- 0.015 sec / 0.000 sec
select 이름, 성
from 사원
where year(입사일자) between '1994' and '2000'
```
![33](https://user-images.githubusercontent.com/48278519/153130272-14d562a7-06eb-4d14-9e79-4910a9e648ad.png)
- 인덱스 없이 테이블에 직접 접근하며 한 번에 다수의 페이지에 접근하므로 더 효율적


### # Conculusion
- ```인덱스 스캔으로 잦은 랜덤 엑세스 부하 발생한다면 인덱스를 활용하지 않는 편이 좋을 수 있다.```

<br>
<br>
<br>

# 3. 테이블 조인 설정 변경으로 SQL 튜닝하기
## 1) 부서의 시작일자가 '2002-03-01'이후인 사원 데이터를 조회하는 쿼리
### # AS_IS
```sql
-- 5.266 sec / 2.031 sec
select 매핑.사원번호,
	   부서.부서번호
from 부서사원_매핑 매핑,
	  부서
where 매핑.부서번호 = 부서.부서번호
  and 매핑.시작일자 >= '2002-03-01';
```
![34](https://user-images.githubusercontent.com/48278519/153130579-531dd663-a630-4ffa-95f2-09b45ebe1caa.png)
- 부서 테이블에 먼저 접근 후 UI_부서명 index를 활용해 index full scan
- 상대적으로 큰 크기의 부서사원매핑 테이블은 I_부서번호 index로 index scan 수행( 4만건의 행을 인덱스 스캔을 하고 랜덤 엑세스로 테이블에 접근)


### # Tunning
- 위의 실행 예시처럼 드리븐 테이블에서 대량 데이터에 랜덤 액세스시 비효율적
- 부서사원_매핑 테이블에 30만 건 이상의 데이터를 MySQL 엔진으로 가져온 모든 데이터에 대해 where 절의 필터 조건(매핑.시작일자) 수행하기 때문에 **매핑.시작일자 조건절을 먼저 적용할 수 있다면 조인 시 비교 대상 줄어듬**


### # TO_BE
```sql
-- 0.328 sec / 0.062 sec
select straight_join
	   매핑.사원번호,
       부서.부서번호
  from 부서사원_매핑 매핑,
  	   부서
 where 매핑.부서번호 = 부서.부서번호
   and 매핑.시작일자 >= '2002-03-01';
```
![35](https://user-images.githubusercontent.com/48278519/153130661-d9329688-101b-494e-bde8-14f91f0fc994.png)
- straight_join 힌트를 사용하여, from절에 작성된 테이블 순서대로 조인에 참여할 수 있도록 고정
- 대용량인 부서사원_매핑 테이블을 먼저 접근하여 테이블 풀 스캔으로 한번에 다수 페이지 접근 후, 부서 테이블에선 primary key로 1개 데이터에 접근하는 방식


### # Conculusion
- ```작은 테이블이 먼저 조인에 참여하지 않도록 한다.```

<br>
<br>

## 2) 사원번호가 450,000 보다 크고 그동안 받은 연봉 중 한번이라도 100,000 달러를 초과한 적이 있는 사원 정보를 찾는 쿼리
### # AS_IS
```sql
-- 0.172 sec / 0.516 sec
select 사원.사원번호, 사원.이름, 사원.성
from 사원
where 사원번호 > 450000
  and (select max(연봉)
  		from 급여
        where 사원번호 = 사원.사원번호) > 100000;
```
![36](https://user-images.githubusercontent.com/48278519/153130784-82213c72-5db2-4ce9-ae69-4b538172aed4.png)
- 사원테이블은 primary key를 활용해서 index range scan 수행, 그 후 급여 테이블은 외부 테이블인 사원테이블로부터 조건을 전달받아 수행하는 의존 서브 쿼리(dependent subquery)로 수행 


### # Tunning
- select_type 항목에 DEPENDENT 키워드가 있으면, 외부 테이블에서 조건절을 받은 뒤 처리되어야 하므로 튜닝 대상으로 고려
- 외부 테이블인 사원 테이블의 사원 정보를 서브쿼리 대신 조인으로 변경


### # TO_BE
```sql
-- 0.156 sec / 0.516 sec
select 사원.사원번호,
	   사원.이름,
       사원.성
  from 사원,
  	   급여
 where 사원.사원번호 > 450000
   and 사원.사원번호 = 급여.사원번호
group by 사원.사원번호
having max(급여.연봉) > 100000;
```
![37](https://user-images.githubusercontent.com/48278519/153130834-bc9e2520-02c9-4fab-800f-4eeecd84115b.png)
- 드라이빙 테이블은 급여 테이블, 드리븐 테이블은 사원 테이블
- 급여 테이블에 먼저 접근하기 위한 범위 축소 조건은 사원.사원번호 > 450000 절을 통해 급여.사원번호 > 450000 조건절로 변형되어 적용
- DEPENDENT 방식은 삭제되고, 사원 테이블과 급여 테이블이 조인하는 방식으로 변경


### # Conculusion
- ```메인 테이블에 의존하는 DEPENDENT 쿼리 방식은 사용하지 않도록 한다.```

<br>
<br>

## 3) A 출입문으로 출입한 사원이 총 몇명인지 구하는 쿼리
### # AS_IS
```sql
-- 30.594 sec / 0.000 sec
select count(distinct 사원.사원번호) as 데이터 건수
from 사원,
    ( select 사원번호
        from 사원출입기록 기록
       where 출입문 = 'A') 기록
where 사원.사원번호 = 기록.사원번호
```
![38](https://user-images.githubusercontent.com/48278519/153130954-07adfb3c-fd12-46db-aeb4-63b7841a7aa7.png)
- 사원출입기록 테이블은 값이 'A'인 상수와 직접 비교하므로 ref 항목이 const, 인덱스를 사용한 동등 비교 수행하므로 type 항목이 ref로 표시
- 사원 테이블은 primary key를 사용해서 조인 조건절인 사원번호 컬럼으로 데이터 비교하기 때문에 type 항목에 eq_ref로 표시 


### # Tunning
```sql
select count(distinct 기록.사원번호) as 데이터건수
 from 사원,
      사원출입기록 기록
 where 사원.사원번호 = 기록.사원번호
   and 출입문 = 'A';
 ```
- from 절의 인라인 뷰는 옵티마이저에 의해 view merging으로 최적화 되어 위의 SQL문처럼 수행됨
- select 절의 최종 결과는 사원번호에서 중복을 제거한 건수만 알면 됨<br>
=> 사원출입기록의 사원번호는 중복제거를 하기 보단 사원 테이블과 조인 과정 중 값의 존재 여부만 확인해도 됨<br>
=> EXISTS 사용 가능


### # TO_BE
```sql
-- 0.641 sec / 0.000 sec
select count(1) as 데이터건수
from 사원
where exists ( select 1
				from  사원출입기록 기록
                where 출입문 = 'A'
              and 기록.사원번호 = 사원.사원번호)
```
 ![39](https://user-images.githubusercontent.com/48278519/153130968-f265b686-3276-4cae-8c3d-1c95f5ed804d.png)
- 사원출입기록 테이블의 데이터는 최종결과에 사용하지 않고 존재 여부만 파악하면 되므로 EXISTS 구문으로 변경
- 실행계획을 보면, id가 1인 사원 테이블은 드라이빙 테이블이고, <subquery2>는 id = 2인 사원출력기록 테이블
- 사원출력기록 테이블은 EXISTS 연산자로 데이터 존재 여부를 파악하기 위해 임시 테이블을 생성하는 MATERIALIZED로 표기

### # Conculusion
- ```불필요한 조인을 수행하면 안된다.```

<br>
<br>
<br>

# 4. 정리
## 1. SQL문 단순 수정으로 튜닝하기
- 기본키를 변형하지 않도록 한다.
- 쿼리 실행 시 불필요한 함수를 호출하지 않도록 한다.
- 형 변환으로 인덱스를 활용하지 못하는 예를 없애도록 한다.
- 열을 결합하여 사용하지 않는것이 좋다.(결합하려는 열이 인덱스가 존재하는 경우)
- 불필요한 distinct는 하지 않아야 한다.
- 불필요한 union(중복제거)처리는 하지 않는다.
- 인덱스의 열을 고려하여 정렬 작업을 수행해야 한다.
- 더 효율적인 인덱스를 사용할 수 있는지에 대한 검토가 필요하다.
- 전체 데이터 대비 조회 결과 데이터 비율이 높다면, 인덱스를 활용하지 않는 편이 좋을 수 있다.
- 인덱스 스캔으로 잦은 랜덤 엑세스 부하 발생한다면 인덱스를 활용하지 않는 편이 좋을 수 있다.
  
  
## 2. 테이블 조인 설정 변경으로 튜닝하기
- 작은 테이블이 먼저 조인에 참여하지 않도록 한다.
- 메인 테이블에 의존하는 DEPENDENT 쿼리 방식은 사용하지 않도록 한다.
- 불필요한 조인을 수행하면 안된다.

<br>
<br>
<br>
	
# 5. 출처
- 업무에 바로 쓰는 SQL 튜닝
