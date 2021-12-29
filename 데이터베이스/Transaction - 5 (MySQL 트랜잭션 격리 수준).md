트랜잭션의 격리 수준(Isolation level)이란 동시에 여러 트랜잭션이 처리될 때, 특정 트랜잭션이 다른 트랙잭션에서 변경하거나 조회하는 데이터를 볼 수 있도록 허용할지 말지를 결정하는 것이다.

격리 수준은 다음과 같이 크게 4가지로 정의할 수 있다.

- READ UNCOMMITTED (커밋되지 않은 읽기)
- READ COMMITTED (커밋된 읽기)
- REPEATABLE READ (반복 가능한 읽기)
- SERIALIZABLE (직렬화 가능)

`DIRTY READ` 라고도 하는 `READ UMCOMMITTED` 는 일반적인 데이터베이스에서는 거의 사용하지 않고, `SERIALIZABLE` 또한 동시성이 중요한 데이터베이스에서 거의 사용되지 않는다.

4개의 격리 수준에서 순서대로 뒤로 갈수록 각 트랜잭션 간의 데이터 격리(고립) 정도가 높아지며, 동시 처리 성능이 떨어진다. 그러나 `SERIALIZABLE` 격리 수준이 아니라면 크게 성능의 개선이나 저하는 발생하지 않는다.

데이터베이스의 격리 수준을 이야기하면 항상 함께 언급되는 세 가지 부정합의 문제점이 있다. 이 세가지 부정합의 문제는 아래의 표처럼 격리 수준의 레벨에 따라 발생할 수도 있고 발생하지 않을 수도 있다.  

![image](https://user-images.githubusercontent.com/55661631/147658443-652b1f2c-8b69-4bb1-8b1d-da34059adc04.png)

> **[SQL-92](https://en.wikipedia.org/wiki/SQL-92)** 또는 **[SQL-99](https://crate.io/docs/sql-99/en/latest/)** 표준에 따르면 `REPEATABLE READ` 격리 수준에서는 `PHANTOM READ`가 발생할 수 있으나 `InnoDB`에서는 독특한 특성 때문에 `REPEATABLE READ` 격리 수준에서도 `PHANTOM READ`가 발생하지 않는다.
> 

격리 수준에서 발생할 수 있는 세 가지 부정합 문제는 각 격리 수준별 설명에서 소개하겠다. 참고로 일반적인 온라인 서비스 용도의 데이터베이스는 `READ COMMITTED`와 `REPEATABLE READ` 둘 중 하나의 전략을 사용한다. 오라클은 `READ COMMITTED`, MySQL은 `REPEATABLE READ`를 주로 사용한다.

# READ UNCOMMITTED

`READ UNCOMMITTED` 격리 수준에서는 각 트랜잭션에서의 변경 내용이 `COMMIT`이나 `ROLLBACK` 여부에 상관 없이 다른 트랜잭션에서 보여지게 된다.

## 더티 리드(Dirty Read) 발생

> **더티 리드(Dirty Read)란?**
다른 트랜잭션에서 처리한 작업이 완료되지 않았음에도 불구하고 다른 트랜잭션에서 볼 수 있게 되는 현상을 더티 리드(Dirty Read)라 한다.
> 

아래 그림은 다른 트랜잭션에서 사용자 B가 실행하는 `SELECT` 쿼리의 결과에 사용자 A의 `INSERT` 쿼리가 `COMMIT` 되기 전에 어떤 영향을 미치는지 보여주는 예시이다.

![image](https://user-images.githubusercontent.com/55661631/147658467-7f9e3850-ef59-4ac9-acf1-64595a5a7ec9.png)
 

사용자 A는 `emp_no = 50000`, `first_name = 'JuBal'`인 새로운 사원을 삽입하고 있다. 그리고 사용자 B는 사용자 A가 변경된 내용을 커밋하기도 전에 `emp_no = 50000`인 사원을 검색하고 있다. 그러나 사용자 B의 `SELECT` 쿼리 결과에서는 사용자 A가 삽입한 새로운 사원(커밋되지 않은)이 조회된다. 

여기서 문제는 만약 **사용자 A가 작업 도중 문제가 발생하여 삽입한 내용을 롤백해도 사용자 B는 `JuBal`이 정상적인 사원이라 판단하고 계속해서 처리**하게 되는 것이다.

이처럼 어떠한 **트랜잭션에서 처리한 작업이 완료되지 않았음에도 불구하고 다른 트랜잭션에서 볼 수 있게 되는 현상을 더티 리드**라 하고, **더티 리드가 허용되는 격리 수준은 `READ UNCOMMITTED`이다**.

더티 리드 현상은 데이터가 나타났다가 사라졌다 하는 현상을 초래할 수 있으므로 개발자와 사용자를 상당히 혼란스럽게 만들기 때문에 더티 리드를 유발하는 `READ UNCOMMITTED` 격리 수준은 트랜잭션의 격리 수준으로 인정하지 않을 정도로 정합성에 문제가 많은 격리 수준이다. 따라서 MySQL을 사용한다면 **최소 `READ COMMITTED` 이상의 격리 수준을 사용할 것을 권장한다.**

# READ COMMITTED

`READ COMMITTED` 격리 수준은 오라클에서 기본적으로 사용되고 있으며 온라인 서비스에서 **가장 많이 선택되는 격리 수준**이다. 이 레벨에서는 위 `READ UNCOMMITTED` 수준에서 발생할 수 있는 **더티 리드(Dirty Read)**와 같은 현상은 발생하지 않는다. 어떠한 트랜잭션에서 데이터를 변경하더라도 커밋이 완료된 데이터만 다른 트랜잭션에서 조회할 수 있기 때문이다. 그러나 `**READ COMMITTED` 격리 수준에서는 `NON-REPEATABLE READ`라는 부정합 문제가 존재**한다.

## 더티 리드(Dirty Read) 해결

아래 그림은 `READ COMMITTED` 격리 수준에서 사용자 A가 변경한 내용이 사용자 B에게 어떻게 조회되는지 보여준다.

![image](https://user-images.githubusercontent.com/55661631/147658506-cbbb7f61-90c3-48cf-b1be-4a1eea7b835c.png)

사용자 A는 `emp_no = 50000`인 사원의 `first_name`을 "JuBal"에서 "Toto"로 수정했는데, 이 때 **새로운 값인 "Toto"는 `employees` 테이블에 즉시 기록되고 이전 값인 "JuBal"은 `Undo` 영역으로 백업**이 된다. 

따라서 사용자 A가 이러한 변경 내역을 커밋하기전에 사용자 B가 `emp_no = 50000`인 사원을 조회하면 결과값은 "Toto"가 아닌 이전 값인 "**JuBal**"이 조회가 된다. 여기서 사용자 B의 `SELECT` 쿼리 결과는 `employees` 테이블이 아닌 **`UNDO` 영역의 백업된 레코드에서 가져온 결과**이다.

**`READ COMMITTED`** 격리 수준에서는 어떤 트랜잭션에서 변경한 내용이 커밋되기 전까지는 다른 트랜잭션에서 그러한 변경 내역을 조회할 수 없기 때문이다.

최종적으로 사용자 A가 변경된 내용을 커밋하면 그때부터는 다른 트랜잭션에서도 백업된 UNDO영역의 데이터인 "JuBal"이 아닌 새롭게 변경된 "Toto"라는 값을 참조할 수 있게 된다.

> **언두(Undo) 로그**
언두 영역은 UPDATE 문장이나 DELETE와 같은 문장으로 데이터를 변경했을 때 변경되기 전의 데이터(이전 데이터)를 보관하는 곳이다.

이러한 언두 데이터는 크게 두 가지 용도로 사용된다.
1. 트랜잭션의 롤백 대비용
2. 트랜잭션의 격리 수준을 유지하면서 높은 동시성을 제공
> 

## NON-REPEATABLE READ 발생

> **NON-REPEATABLE READ란?**
하나의 트랜잭션 내에서 동일한 `SELECT` 쿼리를 실행했을 때 항상 같은 결과를 보장해야 한다는 `REPEATABLE READ` 정합성에 어긋나는 것을 `NON-REPEATABLE READ`라고 한다.
> 

![image](https://user-images.githubusercontent.com/55661631/147658599-8395ec06-b43e-4196-a72a-b7c953c4e419.png)

위 그림에서 사용자 B가 `BEGIN` 명령으로 트랜잭션을 시작하고 `first_name = 'Toto'`인 사원을 조회하면 일치하는 데이터가 존재하지 않는다. 하지만 이후에 사용자 A가 `emp_no = 50000`인 사원의 이름을 'Toto'로 수정하고 커밋한 후 사용자 B가 똑같은 `SELECT` 쿼리로 조회하면 이번에는 결과가 1건이 조회가 된다. 이는 별다른 문제는 없어보이나 사용자 B가 하나의 트랜잭션내에서 **동일한 SELECT 쿼리를 실행했을 때 항상 같은 결과를 보장해야 한다는 `REPEATABLE READ` 정합성에 어긋**나게 된다.

이러한 부정합 현상은 일반적인 웹 애플리케이션에서는 크게 문제가 되지않지만, 하나의 트랜잭션에서 동일한 데이터를 여러 번 읽고 변경하는 작업이 **금전적인 처리**와 연결되면 문제가 될 수 있다.

예를 들어, 다른 트랜잭션에서 입금과 출금 처리가 계속 진행되고 있을 때 **다른 트랜잭션에서 오늘 입금된 금액의 총합을 조회**한다고 가정하겠다. 이때 `READ COMMITTED` 격리 수준을 사용한다면 `REPEATABLE READ`가 보장되지 않기 때문에 총합을 계산하는 `SELECT` 쿼리를 실행할 때마다 다른 결과를 가져오는 큰 문제가 발생하게 될 것이다.

# REPEATABLE READ

`REPEATABLE READ`는 ****MySQL의 InnoDB 스토리지 엔진에서 기본적으로 사용되는 격리 수준이다. 이 격리 수준에서는 `READ COMMITTED` 격리 수준에서 발생하는 **`NON-REPEATABLE READ`** 부정합이 발생하지 않지만, `PHANTOM READ` 부정합이 발생한다.

## NON-REPEATABLE READ 해결

`REPEATABLE READ`는 언두(Undo) 영역에 백업된 이전 데이터를 통해 트랜잭션 내에서는 동일한 결과를 보여줘 `NON-REPEATABLE READ` 문제가 발생하지 않도록 보장해준다. 

사실 `READ COMMITTED` 격리 수준 또한 언두 영역에 백업된 이전 데이터를 보여주나 `REPEATABLE READ` 격리 수준과의 차이는 ‘언두 영역에 백업된 레코드의 여러 버전 가운데 몇 번째 버전을 보여주냐’에 차이가 있어 `NON-REPEATABLE READ` 문제를 해결할 수 있다.

쉽게 말하자면, 언두 영역에 백업된 모든 데이터에는 변경을 발생시킨 트랜잭션의 번호가 포함되어 있는데, `REPEATABLE READ` 격리 수준에서는 실행 중인 트랜잭션보다 작은 트랜잭션에서 변경한 것만 보게 되어 `NON-REPEATABLE READ` 문제를 해결한다.

쉬운 이해를 위해 그림과 함께 예시를 살펴보겠다.

![image](https://user-images.githubusercontent.com/55661631/147658623-92fd1396-3ed5-4429-8101-451cc86b595b.png)

먼저 `employees` 테이블은 번호가 6인 트랜잭션에 의해 ‘JuBal’ 사원이 삽입되었다고 가정하겠다.

위 그림은 사용자 A가 `emp_no = 50000`인 사원의 이름을 변경하는 과정에서 사용자 B가 `emp_no = 50000`인 사원을 `SELECT`할 때 `REPEATABLE READ` 격리 수준이 작동하는 방식을 보여준다.

사용자 A의 트랜잭션 번호는 12이고, 사용자 B의 트랜잭션 번호는 10이다. 이때 사용자 A는 사원의 이름을 'Toto'로 변경하고 커밋을 수행한다. 그런데 사용자 B는 `emp_no = 50000`인 사원을 A 트랜잭션이 변경을 실행하기 전과 실행한 후 각각 조회를 했지만, 데이터는 항상 동일한 'JuBal'라는 결과가 나온다. 

그 이유는 사용자 B가 BEGIN 명령으로 트랜잭션을 시작하면서 10번이라는 트랜잭션 번호를 부여받았는데, 그때부터 사용자 B의 10번 트랜잭션 안에서 실행되는 모든 `SELECT` 쿼리는 자신의 트랜잭션인 10번 보다 작은 트랜잭션 번호에서 변경한 것만 보기 때문이다.

## PHANTOM READ 발생

> **PHANTOM READ란?**
`SELECT ... FOR UPDATE` 쿼리와 같은 쓰기 잠금을 거는 경우 다른 트랜잭션에서 수향한 변경 작업에 의해 레코드가 보였다 안 보였다 하는 현상을 `PHANTOM READ`라고 한다.
> 

![image](https://user-images.githubusercontent.com/55661631/147658645-775419af-315f-4e88-b82b-b92f2e9752fd.png)

위 그림은 사용자 A가 `employees` 테이블에 `INSERT`를 실행하기 전과 후에 사용자 B가 `SELECT .. FOR UPDATE` 쿼리로 `employees` 테이블을 조회했을 때의 결과이다.

‘NON-REPEATABLE READ 해결’에서 설명한 것처럼 동일한 트랜잭션 내에서의 동일한 쿼리는 항상 같은 결과를 출력해야 한다. 그러나 위 그림에서 두 번의 `SELECT .. FOR UPDATE`는 다른 결과를 보여주고 있다.  그 이유는 `SELECT .. FOR UPDATE` 쿼리의 경우 `SELECT` 하는 레코드에 쓰기 잠금을 걸어야 하는데, 언두(Undo) 영역에는 잠금을 걸 수 없기 때문이다. 

따라서, `SELECT .. FOR UPDATE`나 `SELECT ... LOCK IN SHARE MODE`로 조회되는 레코드는 언두 영역의 변경 전 데이터를 가져오는 것이 아니라 현재 레코드의 값을 가져오는 것이다.

# SERIALIZABLE

가장 단순한 격리 수준이면서 동시에 가장 엄격한 격리 수준이다. 그만큼 동시 처리 성능도 다른 트랜잭션 격리 수준보다 떨어진다.

`InnoDB` 테이블에서 순수한 `SELECT` 작업은 아무런 레코드 잠금도 설정하지 않고 실행되지만, 트랜잭션 격리 수준이 `SERIALIZABLE`로 설정되면 읽기 작업도 공유 잠금(읽기 잠금)을 획득해야 한다. 즉, 한 트랜잭션에서 읽고 쓰는 레코드를 다른 트랜잭션에서는 절대 접근할 수 없는 것이다.

따라서, `SERIALIZABLE` 격리 수준에서는 모든 부정합 문제가 발생하지 않는다. 그러나 `InnoDB` 스토리지 엔진에서는 갭 락과 넥스트 키 락 덕분에 `REPEATABLE READ` 격리 수준에서도 이미 `PHANTOM READ` 문제가 발생하지 않기 때문에 굳이 `SERIALIZABLE`을 사용할 필요는 없어 보인다.

# 정리

- **READ UNCOMMITTED**
    - 한 트랜잭션의 변경된 내용을 커밋이나 롤백과 상관 없이 다른 트랜잭션에서 읽을 수 있는 격리 수준
    - 모든 부정합 문제 발생
- **READ COMMITTED**
    - COMMIT이 완료된 데이터만 조회 가능한 격리 수준
    - 더티 리드 해결
- **REPEATABLE READ**
    - 트랜잭션이 시작되기 전에 커밋된 내용에 관해서만 조회할 수 있는 격리 수준
    - NON-REPEATABLE-READ 해결
- **SERIALIZABLE**
    - 한 트랜잭션을 다른 트랜잭션으로부터 완전히 분리하는 격리 수준
    - 모든 부정합 문제 해결

# 참고

- [https://zzang9ha.tistory.com/381](https://zzang9ha.tistory.com/381)
- [https://wbluke.tistory.com/44](https://wbluke.tistory.com/44)
- [https://willseungh0.tistory.com/146](https://willseungh0.tistory.com/146)
- [https://willseungh0.tistory.com/145?category=874332](https://willseungh0.tistory.com/145?category=874332)
- [https://velog.io/@yu-jin-song/DB-트랜잭션-격리-수준Transaction-Isolation-Level](https://velog.io/@yu-jin-song/DB-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98-%EA%B2%A9%EB%A6%AC-%EC%88%98%EC%A4%80Transaction-Isolation-Level)
- Real MySQL
