# MyISAM과 Memory 스토리지 엔진의 잠금

`MyISAM`이나 `MEMORY` 스토리지 엔진은 자체적인 잠금을 가지지 않고 `MySQL` 엔진에서 제공하는 테이블 잠금을 그대로 사용한다.그리고 쿼리 단위로 필요한 테이블 잠금을 한번에 요청해서 획득하기 때문에 교착상태가 발생할 수 없다. 

## 잠금 획득

**읽기 잠금**

테이블에 쓰기 잠금이 걸려 있지 않으면 바로 읽기 잠금을 획득하고 읽기 작업을 시작할 수 있다.

**쓰기 잠금**

테이블에 아무런 잠금이 걸려 있지 않아야만 쓰기 잠금을 획득할 수 있고, 그렇지 않다면 다른 잠금이 해제될 때까지 대기해야 한다.

## 잠금 튜닝 판단

테이블 잠금에 대한 작업 상황은 `MySQL`의 상태 변수를 통해 확인할 수 있다.

```sql
show status like 'Table%';
```

결과는 다음과 같다.

![image](https://user-images.githubusercontent.com/55661631/147072411-a49a3151-8978-49f7-9e85-ca74c91eeab9.png)

- `Table_locks_immediate`
    - 다른 잠금이 풀리기를 기다리지 않고 바로 잠금을 획득한 횟수
- `Table_locks_waited`
    - 다른 잠금이 이미 해당 테이블을 사용하고 있어서 기다려야 했던 횟수

현재 사용 중인 `MySQL` 서버에서 `MyISAM`이나 `MEMORY` 테이블을 사용하고 있다면 위의 상태 값들의 비율을 비교해 테이블 잠금을 대기하는 쿼리가 어느 정도 인지 확인할 수 있다.

```sql
 잠금 대기 쿼리 비율 = Table_locks_waited / (Table_locks_immediate + Table_locks_waited) * 100;
```

만약 잠금 대기 쿼리 비율 수치가 높고 테이블  잠금 때문에 경합이 많이 발생하고 있으면, 테이블을 분리하거나 `InnoDB` 스토리지 엔진으로 변환하는 방법을 고려하는 것이 좋다.

`InnoDB` 스토리지 엔진의 경우에는 레코드 단위의 잠금을 사용하기 때문에 위 수치에 반영되지 않는다. 집계된 수치는 `MyISAM`이나 `MEMORY` 스토리지 엔진을 사용하는 테이블이 대상이 된다.

# InnoDB 스토리지 엔진의 잠금

`InnoDB` 스토리지 엔진은 레코드 기반의 잠금 기능을 제공하며, 잠금 정보가 상당히 작은 공간으로 관리되기 때문에 레코드 락이 페이지 락으로, 또는 테이블 락으로 레벨업되는 경우는 없다.

일반 사용 `DBMS` 와는 조금 다르게 `InnoDB` 스토리지 엔진에서는 레코드 락뿐 아니라 레코드와 레코드 사이의 간격을 잠그는 갭 락이라는 것이 존재한다.

![image](https://user-images.githubusercontent.com/55661631/147072442-584f3ed3-9056-4cd4-8330-20aabee9c3d9.png)

## 레코드 락(Record lock, Record only lock)

레코드 자체만을 잠그는 것을 레코드 락이라고 하며, 다른 상용 DBMS의 레코드 락과 동일한 역할을 한다. 

한 가지 중요한 차이는 `InnoDB` 스토리지 엔진은 레코드 자체가 아니라 인덱스의 레코드를 잠근다는 점이다. 만약 인덱스가 하나도 없는 테이블이라 하더라도 내부적으로 자동 생성된 클러스터 인덱스를 이용해 잠금을 설정하기 때문에 문제가 없다. 레코드 자체를 잠그느냐, 아니면 인덱스를 잠그느냐는 상당히 크고 중요한 차이를 만들어 내기 때문에 밑에서 자세히 설명하겠다.

`InnoDB`에서는 대부분 보조 인덱스를 이용한 변경 작업은 넥스트 키 락(Next key lock) 또는 갭 락(Gap lock)을 사용하지만, 프라이머리 키 또는 유니크 인덱스에 의한 변경 작업은 갭(Gap, 간격)에 대해서는 잠그지 않고 레코드 자체에 대해서만 락을 건다.

## 갭 락

`InnoDB`의 다른 `DBMS`와의 또 다른 차이가 바로 갭 락이라는 것이다. 갭 락은 레코드 그 자체가 아니라 레코드와 바로 인접한 레코드 사이의 간격만을 잠그는 것을 의미한다. 갭 락의 역할은 레코드와 레코드 사이의 간격에 새로운 레코드가 생성(INSERT)되는 것을 제어하는 것이다. **갭 락이라는 것은 개념일 뿐이지 자체적으로 사용되지는 않고, 넥스트 키 락의 일부로 사용**된다. 

## 넥스트 키 락

레코드 락과 갭 락을 합쳐 놓은 형태의 잠금을 넥스트 키 락이라고 한다. `STATEMENT` 포맷의 바이너리 로그를 사용하는 `MySQL` 서버에서는 `REPEATABLE READ` 격리 수준을 사용해야 한다. 또한 `Innodb_locks_unsafe_for_binlog` 파라미터가 비활성화되면(0으로 설정) 변경을 위해 검색하는 레코드에는 넥스트 키 락 방식으로 잠금이 걸린다. 

`InnoDB`의 갭 락이나 넥스트 키 락은 바이너리 로그에 기록되는 쿼리가 레플리카(슬레이브) 서버에서 실행될 때 소스(마스터) 서버에서 만들어 낸 결과와 동일한 결과를 만들어내도록 보장하는 것이 주 목적이다. 

그런데 의외로 넥스트 키 락과 갭 락으로 인해 데드락이 발생하거나 다른 트랜잭션을 기다리게 만드는 일이 자주 발생하기 때문에 가능하면 바이너리 로그 포맷을 `ROW` 형태로 바꿔서 넥스트 키 락이나 갭 락을 줄이는 것이 좋다.

## 자동 증가 락

`MySQL`에서는 자동 증가하는 숫자 값을 추출하기 위해 `AUTO_INCREMENT`라는 칼럼 속성을 제공한다. 

`AUTO_INCREMENT` 칼럼이 사용된 테이블에서 동시에 여러 레코드가 `INSERT`되는 경우, 저장되는 각 레코드는 중복되지 않고 저장된 순서대로 증가한 일련번호 값을 가져야 한다. InnoDB 스토리지 엔진에서는 이를 위해 내부적으로 자동 증가 락이라고 하는 테이블 수준의 잠금을 사용한다.

`AUTO_INCREMENT 락`은 `INSERT`와 `REPLACE` 쿼리 문장과 같은 새로운 레코드를 저장하는 쿼리에서만 필요하며, `UPDATE`나 `DELETE` 등의 쿼리에는 걸리지 않는다. `InnoDB`의 다른 잠금(레코드 락이나 넥스트 키 락)과는 달리, 자동 증가 락은 트랜잭션과 관계없이 `INSERT`나 `REPLACE` 문장에서 `AUTO_INCREMENT` 값을 가져오는 순간만 자동 증가 락이 걸렸다가 즉시 해제된다. 

자동 증가 락은 테이블마다 단 하나만 존재하기 때문에 두 개의 `INSERT` 쿼리가 동시에 실행되는 경우 하나의 쿼리가 자동 증가 락을 걸게 되면 나머지 쿼리는 자동 증가 락을 기다려야 한다.

자동 증가 락은 명시적으로 획득하고 해제하는 방법은 없고, 아주 짧은 시간 동안만 걸렸다가 해제되는 잠금이라서 대부분의 경우 문제가 되지 않는다.

자동 증가 락에 대한 지금까지의 설명은 `MySQL 5.0` 이하 버전에 국한된 것이었다. `MySQL 5.1` 이상부터는 `innodb_autoinc_look_mode`라는 파라미터를 이용해 자동 증가 락의 작동 방식을 변경할 수 있다.

- **innodb_autonic_lock_mode=0**
    - `MySQL 5.0`과 동일한 잠금 방식으로 모든 INSERT 문장은 자동 증가 락을 사용한다.
- **innodb_autonic_lock_mode=1**
    - 단순히 한 건 또는 여러 건의 데이터를 `INSERT`하는 `SQL` 중에서 `MySQL` 서버가 `INSERT` 되는 레코드의 건수를 정확히 예측할 수 있을 때는 자동 증가 락을 사용하지 않고, 훨씬 가볍과 빠른 래치(뮤텍스)를 이용해 처리한다.
    - 하지만 `INSERT ... SELECT`와 같이 `MySQL` 서버가 건수를 쿼리를 실행하기 전에 예측할 수 없을 때는 `MySQL 5.0`에서와 같이 자동 증가 락을 사용하게 된다.
- **innodb_autonic_lock_mode=2**
    - `InnoDB` 스토리지 엔진은 절대 자동 증가 락을 걸지 않고 항상 경량화된 래치(뮤텍스)를 사용한다.

## 인덱스와 잠금

`InnoDB`의 잠금과 인덱스는 중요한 연관 관계가 있기 때문에 자세히 살펴보려 한다.

레코드 락을 설명하면서 잠깐 언급했듯이 `InnoDB`의 잠금은 레코드를 잠그는 것이 아니라 인덱스를 잠그는 방식으로 처리한다. 즉, 변경해야 할 레코드를 찾기 위해 검색한 인덱스의 레코드를 모두 잠가야 한다. 정확한 이해를 위해 다음 `UPDATE` 문장을 한번 살펴보겠다.

`employees` 테이블에 `first_name` 컬럼에 인덱스가 걸려 있다. 해당 테이블에서 `first_name` 이 Georgi인 사원은 전체 253명이 있으며, `first_name` 이 Georgi이며, `last_name` 이 Klassen인 사원은 1명만 있는 것을 아래 쿼리로 확인할 수 있다.

```sql
SELECT COUNT(*) FROM employees WHERE first_name='Georgi';

//결과
+---------+
| 253     |
+---------+

SELECT COUNT(*) FROM employees WHERE first_name='Georgi' AND last_name='Klassen';

//결과
+---------+
| 1       |
+---------+
```

다음과 같이 입사 일자를 오늘로 변경하는 쿼리를 실행해보자.

```sql
UPDATE employees SET hire_date=NOW()
        WHERE first_name='Georgi' AND last_name='klassen';
```

`UPDATE` 쿼리가 실행되면 1건의 레코드가 수정될 것이다. 하지만 이 1건의 업데이트를 위해서 몇 개의 레코드에 잠금이 걸릴까?

`UPDATE` 쿼리의 조건에서 인덱스를 이용할 수 있는 조건은 `first_name='Georgi'` 이며, `last_name` 컬럼은 인덱스에 없기 때문에 `first_name='Georgi'`인 레코드 253건에 모두 잠금이 걸린다.

이 예제에서는 몇 건 안되는 레코드만 잠그지만 `UPDATE` 쿼리를 위해 적절히 인덱스가 준비돼 있지 않다면 각 클라이언트 간의 동시성이 상당히 떨어져서 한 세션에서 `UPDATE` 작업을 하는 중에 다른 세션은 그 테이블을 업데이트하지 못하고 기다려야 하는 상황이 발생한다.

![image](https://user-images.githubusercontent.com/55661631/147072508-82828b57-6322-4bef-9989-a8af8142f489.png)

만약 이 테이블에 인덱스가 하나도 없다면 어떻게 될까?

이러한 경우에는 테이블을 풀 스캔하면서 `UPDATE` 작업을 하는데, 이 과정에서 테이블에 있는 30여 만 건의 모든 레코드를 잠그게 된다. 이것이 `MySQL`의 `InnoDB`에서 인덱스 설계가 중요한 이유 중 하나이다.

## 레코드 수준의 잠금 확인 및 해제

`InnoDB` 스토리지 엔진을 사용하는 테이블의 레코드 수준 잠금은 테이블 수준의 잠금보다는 조금 더 복잡하다. 테이블 잠금에서는 잠금의 대상이 테이블 자체이므로 쉽게 문제의 원인이 발견되고 해결될 수 있다. 하지만 레코드 수준의 잠금은 테이블 레코드 각각에 잠금이 걸리므로 그 레코드가 자주 사용되지 않는다면 오랜 시간 동안 잠겨진 상태로 남아 있어도 잘 발견되지 않는다.

### MySQL 5.1 이상의 잠금 확인 및 해제

`MySQL 5.1` 버전부터는 각 트랜잭션이 어떤 잠금을 기다리고 있는지, 기다리고 있는 잠금을 어떤 트랜잭션이 가지고 있는지를 메타 정보를 통해 조회할 수 있다.

`MySQL5.1` 버전부터는 `information_schema` 라는 데이터베이스에 `INNODB_TRX` , `INNODB_LOCKS` , `INNODB_LOCK_WAITS` 테이블에서 확인할 수 있다.

`MySQL 8.0` 버전부터는 `information_schema`의 정보들은 조금씩 제거(Deprecated)되고 있으며, 그 대신 `performance_schema`의 `data_locks`와 `data__lock_waits` 테이블로 대체되고 있다.

# 참고

- [https://blog.yevgnenll.me/posts/mysql-transaction-lock-isolat](https://blog.yevgnenll.me/posts/mysql-transaction-lock-isolate)
- [https://velog.io/@jsj3282/10.-InnoDB-스토리지-엔진의-잠금](https://velog.io/@jsj3282/10.-InnoDB-%EC%8A%A4%ED%86%A0%EB%A6%AC%EC%A7%80-%EC%97%94%EC%A7%84%EC%9D%98-%EC%9E%A0%EA%B8%88)
- [https://blog.yevgnenll.me/posts/mysql-transaction-lock-isolate](https://blog.yevgnenll.me/posts/mysql-transaction-lock-isolate)
- [https://www.letmecompile.com/mysql-innodb-lock-deadlock/](https://www.letmecompile.com/mysql-innodb-lock-deadlock/)
- Real MySQL
