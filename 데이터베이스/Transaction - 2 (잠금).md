# MySQL 엔진의 잠금

`MySQL` 에서 사용되는 잠금은 크게 **스토리지 엔진 레벨**과 **MySQL 엔진 레벨**로 나눌 수 있다. 

MySQL 엔진은 MySQL 서버에서 스토리지 엔진을 제외한 나머지 부분으로 이해하면 된다. MySQL 엔진 레벨의 잠금은 모든 스토리지 엔진에 영향을 미치지만, 스토리지 엔진 레벨의 잠금은 스토리지 엔진 간 상호 영향을 미치지는 않는다.

## 글로벌 락

글로벌 락은 `FLUSH TABLES WITH READ LOCK` 명령으로 획득할 수 있으며, `MySQL` 에서 제공하는 잠금 가운데 가장 범위가 크다. 한 세션에서 글로벌 락을 획득하면 다른 세션에서 `SELECT` 를 제외한 대부분의 `DDL` 문장이나 `DML` 문장을 실행하는 경우 글로벌 락이 해제될 때까지 대기 상태로 남는다. 

글로벌 락이 영향을 미치는 범위는 `MySQL` 서버 전체이며, 작업 대상 테이블이나 데이터베이스가 다르더라도 동일하게 영향을 미친다. 주로 **데이터베이스에 존재하는 `MyISAM` 이나 `MEMEORY` 테이블에 대해 `mysqldump` 로 일관된 백업을 받아야 할 때 사용**한다.

## 백업 락

위에서 말한 글로벌 락은 `MySQL` 서버의 모든 변경 작업을 멈춘다. `MySQL` 서버가 업그레이드하면서 `MyISAM` 이나 `MEMORY` 스토리지 엔진보다는 `InnoDB` 스토리지 엔진의 사용이 일반화됐다. `InnoDB` 스토리지 엔진은 트랜잭션을 지원하기 때문에 일관된 데이터 상태를 위해 모든 데이터 변경 작업을 멈출 필요는 없다. 그래서 `MySQL 8.0` 부터는 조금 더 가벼운 글로벌 락인 **백업 락**이 도입됐다.

백업 락은 일반적인 테이블의 데이터 변경은 허용된다.

주로 백업은 레플리카 서버에서 실행되는데, 백업이 글로벌 락을 획득하면 복제는 백업 시간만큼 지연될 수 있다. 레플리카 서버에서 백업을 실행하는 도중에 소스 서버에 문제가 생기면 레플리카 서버의 데이터가 최신 상태가 될 때까지 서비스를 멈춰야 할 수도 있다. 

물론 `XtraBackup` 이나 `Enterprise Backup` 과 같은 백업 툴들은 글로벌 락 상황에도 쓰기가 가능해 복제를 진행하며 일관된 백업이 가능하다. 하지만 백업 도중 `DDL` 명령 하나로 인해 백업이 실패할 수도 있다. **`MySQL` 서버의 백업 락은 이런 목적으로 도입됐으며, 정상적으로 복제는 실행되지만 백업의 실패를 막기 위해 `DDL` 명령이 실행되면 복제를 일시 중지하는 역할**을 한다.

## 테이블 락

테이블 락은 개별 단위로 설정되는 잠금이며, 명시적 또는 묵시적으로 특정 테이블의 락을 획득할 수 있다.

**명시적**으로는 아래의 명령으로 특정 테이블의 락을 획득할 수 있다.

```sql
lock table table_name [ read | write ];
```

**묵시적인** 테이블 락은 `MyISAM` 이나 `MEMORY` 테이블에 데이터를 변경하는 쿼리를 실행하면 발생한다. `MySQL` 서버는 데이터가 변경되는 테이블에 잠금을 설정하고 데이터를 변경한 후, 즉시 잠금을 해제하는 형태로 사용된다. 정리하자면, 묵시적인 테이블 락은 쿼리가 실행되는 동안 자동으로 획득했다가 쿼리가 완료된 후 자동 해체된다. 

하지만 `InnoDB` 테이블의 경우 레코드 기반의 잠금을 제공하기 때문에 묵시적인 테이블 락이 설정되지 않는다. 정확히 말하면 데이터 변경(DML) 쿼리에서는 무시되고 스키마를 변경하는 쿼리(DDL)의 경우에만 발생한다.

직접 테이블 락을 획득해보자.

### **읽기 잠금(READ LOCK)의 동작 특성**

```sql
mysql1> LOCK TABLES messages READ;
mysql1> SHOW PROCESSLIST; -- Query ok
```

```sql
-- 다른 프로세스(세션)에서 실행
mysql2> LOCK TABLES messages WRITE; -- ...
```

`mysql1` 프로세스에서 테이블 전체에 대해 읽기 잠금을 걸고, `mysql2` 프로세스에서 쓰기 잠금을 걸으면 읽기 잠금이 해제될 때까지 기다린다.

```sql
mysql1> SELECT * FROM messages; -- Query ok.
mysql1> LOCK TABLES messages READ;
mysql1> SELECT * FROM messages; -- Query ok.
```

```sql
mysql2> SELECT * FROM messages; -- Query ok.
mysql2> INSERT INTO messages (message) VALUES ('FOO'); -- ...
```

`mysql1` 프로세스가 읽기 잠금을 걸었으므로, `mysql2` 프로세스에서도 읽기는 가능하지만, 쓰기를 시도하면 잠금이 해제될 때까지 기다린다.

```sql
mysql1> INSERT INTO messages (message) VALUES ('BAR'); 
-- Table 'messages' was locked with a READ lock and can't be updated
```

 읽기 잠금을 건 `mysql1` 프로세스도 쓰기가 불가능하다.

**결론**

- `READ`/`WRITE` 무관한게 먼저 테이블을 잠근 프로세스가 잠금을 반환하기 전에 다른 프로세스는 잠금을 얻을 수 없다.
- `READ` 잠금이 걸린 상태에서
    - 다른 프로세스에서 테이블을 읽는 것은 가능하다.
    - `READ` 잠금은 건 프로세스를 포함해 모든 프로세스에서 테이블에 쓰기 작업은 할 수 없다.
    - 잠금을 가진 프로세스에서 쓰기를 시도하면 바로 오류가 발생하지만, 다른 프로세스에서는 잠금이 풀릴 때 까지 기다린다.

### 쓰기 잠금(Write Lock)의 동작 특성

```sql
mysql1> LOCK TABLES messages WRITE;
mysql1> INSERT INTO messages (message) VALUES ('BAZ');
mysql1> SELECT * FROM messages; -- Query ok.
```

```sql
mysql2> SELECT * FROM messages; -- ...
```

`mysql1` 프로세스에서 쓰기 잠금을 걸으면 `mysql2` 프로세스에서는 읽기/쓰기 모두 잠금이 풀릴 때까지 기다린다.

***직접 해보니 블로그의 설명과 결과가 다름 (확인 필요)***

```sql
mysql1> RENAME TABLE messages TO new_messages;
```

`WRITE` 잠금을 가진 `mysql1` 프로세스는 테이블 이름을 변경하는 등의 `DDL` 가능

**결론**

- `WRITE` 잠금을 가진 프로세스에서만 읽기, 쓰기 가능하다.
- 다른 프로세스에서는 잠금이 풀릴 때가지 읽기, 쓰기 모두 불가능하다.

## 네임드 락

네임드 락은 `GET_LOCK()` 함수를 이용해 임의의 문자열에 대해 잠금을 설정할 수 있다. 대상이 테이블이나 레코드 또는 `AUTO_INCREMENT` 와 같은 데이터베이스 객체가 아니고 단순히 사용자가 지정한 문자열(String)에 대해 획득하고 반납(해제)하는 잠금이다. 

```sql
-- "mylock"이라는 문자열에 대해 잠금을 획득한다. 이미 잠금을 사용 중이면 2초 동안만 대기한다.
select get_lock('mylock', 2);

-- "mylock"이라는 문자열에 대해 잠금이 설정돼 있는지 확인한다.
select is_free_lock('mylock');

-- "mylock"이라는 문자열에 대해 획득했던 잠금을 반납(해제)한다.
select release_lock('mylock');

-- 3개 함수 모두 정상적으로 락을 획득하거나 해제한 경우에는 1을, 아니면 NULL이나 0을 반환한다.
```

네임드 락의 경우 많은 레코드에 대해서 복잡한 요건으로 레코드를 변경하는 트랜잭션에 유용하게 사용할 수 있다. 배치 프로그램에서 한꺼번에 많은 레코드를 변경하는 쿼리는 자주 데드락의 원인이 되곤 하는데, 이런 경우 동일 데이터를 변경하거나 참조하는 프로그램끼리 분류해서 네임드 락을 걸로 쿼리를 실행하면 간단히 해결할 수 있다.

`MySQL 8.0` 버전 부터는 네임드 락을 중첨해서 사용할 수 있게 됐으며, 현재 세션에서 획득한 네임드 락을 한 번에 모두 해제하는 기능( `RELEASE_ALL_LOCKS()` )도 추가됐다.

## 메타데이터 락

메타데이터 락은 데이터베이스 객체(대표적으로 테이블이나 뷰 등)의 이름이나 구조를 변경하는 경우에 획득하는 잠금이다. 메타데이터 락은 명시적으로 획득하거나 해제할 수 있는 것이 아니고 `RENAME TABLE tab_a TO tab_b` 같이 테이블의 이름을 변경하는 경우 자동으로 획득하는 잠금이다.

`RENAME TABLE` 명령의 경우 원본 이름과 변경될 이름 두 개 모두 한꺼번에 잠금을 설정한다.

간단한 예를 살펴보자.

```sql
rename table tab_innodb to new_tab_innodb, new_tab_innodb to tab_innodb;
```

위와 같이 `RENAME TABLE` 명령문에 두 개의 `RENAME` 작업을 한꺼번에 실행하면 적용이 가능하다. 

하지만 이 문장을 아래와 같이 2개로 나눠서 실행하면 아주 짧은 시간이지만 `tab_innodb` 테이블이 존재하지 않는 순간이 생기며, 그 순간에 실행되는 쿼리는 `Table not found 'tab_innodb'` 오류를 발생시킨다.

```sql
rename table tab_innodb to new_tab_innodb;
rename table new_tab_innodb to tab_innodb;
```

# 참고

- [https://blog.appkr.dev/cheatsheet/understanding-mysql-db-lock/](https://blog.appkr.dev/cheatsheet/understanding-mysql-db-lock/)
- [https://myinfrabox.tistory.com/75](https://myinfrabox.tistory.com/75)
- [https://neulpeumbomin.tistory.com/23](https://neulpeumbomin.tistory.com/23)
- Real MySQL
