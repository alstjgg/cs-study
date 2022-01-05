# 스프링이 제공하는 트랜잭션(Transaction) 핵심 기술

스프링에서 제공하는 트랜잭션 핵심 기술은 다음과 같다.

- 트랜잭션 동기화
- 트랜잭션 추상화
- AOP를 이용한 트랜잭션 분리

## 1. 트랜잭션 동기화

JDBC를 이용하는 개발자가 직접 여러 개의 작업을 하나의 트랜잭션으로 관리하려면 Connection 객체를 공유하는 등 불필요한 작업들이 많이 생긴다.

스프링은 이런 문제를 해결하기 위해 트랜잭션 동기화(Transaction Synchronization) 기술을 제공한다. 트랜잭션 동기화는 트랜잭션을 시작하기 위한 Connection 객체를 특별한 저장소에 보관해두고 필요할 때 꺼내쓸 수 있도록 하는 기술이다.

트랜잭션 동기화 저장소는 스레드마다 Connection 객체를 독립적으로 관리하기 때문에, 멀티 스레드 환경에서도 충돌이 발생하지 않는다.

```java
public class AccountService {
     
	  public void accountTransfer(String sender, String receiver, int money) throws SQLException{
				// 트랜잭션 동기화 작업 초기화
	      TransactionSynchronizationManager.initSynchronization();

				// Connection 오브젝트 생성, 저장소 바인딩, 참조변수 값 리턴
	      Connection conn = DataSourceUtils.getConnection(dataSource); 

				// 트랜잭션 시작
	      conn.setAutoCommit(false);
	       
	      try {
	          accountDAO.minus(sender, money);
	          accountDAO.plus(receiver, money);
	          conn.commit();
	      } catch (SQLException e) {
	          conn.rollback();
	      } finally{
	          DataSourceUtils.releaseConnection(conn, dataSource); // 커넥션을 닫음
	          // 동기화 작업을 종료하고 저장소를 비운다
	          TransactionSynchronizationManager.unbindResource(this.dataSource);
	          TransactionSynchronizationManager.clearSynchronization();
	      }
	  } else{
	      throw new NoMoneyException();
	  }
}
```

그러나 개발자가 JDBC가 아닌 Hibernate와 같은 기술을 쓴다면 위 JDBC에 종속적인 트랜잭션 동기화 코드에 문제가 발생한다. 이러한 기술 종속적인 문제를 해결하기 위해 스프링은 트랜잭션 관리 부분을 추상화한 기술을 제공한다.

## 2. 트랜잭션 추상화

스프링은 트랜잭션 기술의 공통점을 담은 트랜잭션 추상화 기술을 제공하고 있다. 이를 이용함으로써 애플리케이션에 각 기술마다(JDBC, JPA, Hibernate 등) 종속적인 코드를 이용하지 않고도 일관되게 트랜잭션을 처리할 수 있도록 해주고 있다.

![image](https://user-images.githubusercontent.com/55661631/148204934-ee713cc2-049e-41d3-a1a9-ceeac9c378fb.png)

스프링이 제공하는 트랜잭션 경계 설정을 위한 추상 인터페이스는 PlatformTransactionManager 이다. 예를 들어 만약 JDBC의 로컬 트랜잭션을 이용한다면 DataSourceTxManager를 이용하면 된다.

이제 개발자는 사용하는 기술과 무관하게 PlatformTransactionManager을 통해 트랜잭션을 공유할 수 있다.

```java
public class AccountService{

    @Autowired
    PlatformTransactionManager transactionManager;
     
    public void accountTransfer(String sender, String receiver, int money) throws SQLException{
        
				// 트랜잭션 속성 설정
        TransactionStatus status = transactionManager.getTransaction(new DefaultTransactionDefinition());
         
            try {
                  accountDAO.minus(sender, money);
                  accountDAO.plus(receiver, money);
                } else{
                    throw new NoMoneyException();
                }
                 
                transactionManager.commit(status);
                 
            } catch (SQLException e) {
                transactionManager.rollback(status);
            }
         
    }
}
```

그러나 위 예제는 트랜잭션 관리 코드들이 비즈니스 로직 코드와 결합되어 2가지 책임을 갖고 있다. 스프링에서는 AOP를 이용해 트랜잭션 부분을 핵심 비즈니스 로직과 분리하였다.

## 3. AOP를 이용한 트랜잭션 분리

위에서 보여준 예제는 트랜잭션 코드와 비즈니스 로직 코드가 복잡하게 얽혀있다. 스프링에서는 트랜잭션 로직을 클래스 밖으로 빼내서 별도의 모듈로 만드는 AOP(Aspect Oriented Programming, 관점 지향 프로그래밍)를 고안하였고, 이를 적용한 트랜잭션 어노테이션(@Transaction)을 지원하게 되었다.

```java
public class AccountService{
     
		@Transactional
		public void accountTransfer(String sender, String receiver, int money) throws SQLException{
				accountDAO.minus(sender, money);
				accountDAO.plus(receiver, money);
		}
}
```

이 방식을 **선언적 트랜잭션**이라 부르며, **일반적으로 사용하는 방식**이다.

# @Transactional 속성

`@Transactional` 어노테이션(선언적 트랜잭션)을 사용하면 6가지 속성을 지정해 트랜잭션을 세부적으로 이용할 수 있다.

- readOnly
- isolation
- propagation
- timeout
- rollbackFor, noRollbackFor (트랜잭션 롤백/커밋 예외)

트랜잭션 속성에 대해 자세히 알아보자.

## 1. 트랜잭션 전파: propagation

스프링이 제공하는 선언적 트랜잭션(트랜잭션 어노테이션, @Transactional)의 장점 중 하나는 여러 트랜잭션 적용 범위를 묶어서 커다란 하나의 트랜잭션 경계를 만들 수 있다는 점이다. 

트랜잭션 경계의 시작 지점에서 트랜잭션 전파 속성을 참조해서 해당 범위의 트랜잭션을 어떤 식으로 진행시킬지 결정할 수 있다.

스프링에서 지원하는 트랜잭션 전파 속성은 다음 여섯 가지가 있다.

- **REQUIRED**
    - 디폴트 속성으로써 모든 트랜잭션 매니저가 지원하며, 대개 이 속성이면 충분하다. 미리 시작된 트랜잭션이 있으면 참여하고 없으면 새로 시작한다. 가장 간단하고 자연스러운 트랜잭션 전파 방식이지만 매우 강력하며 유용하다. 만약 REQUIRED 속성일 때 하나의 트랜잭션이 시작된 후 다른 트랜잭션 경계가 설정된 메소드를 호출하면 자연스럽게 같은 트랜잭션으로 묶인다.
- **SUPPORTS**
    - 이미 시작 트랜잭션이 있으면 참여하고, 그렇지 않으면 트랜잭션 없이 진행하게 만든다. 트랜잭션이 없기는 하지만 해당 경계 안에서 Connection 객체나 하이버네이트의 Session 등은 공유를 할 수 있다.
- **MANDATORY**
    - 이미 시작된 트랜잭션이 있으면 참여한다. 하지만 트랜잭션이 시작된 것이 없으면 새로 시작하는 대신 예외를 발생시킨다. 혼자서 독립적으로 트랜잭션을 진행하면 안되는 경우에 사용할 수 있다.
- **REQUIRES_NEW**
    - 항상 새로운 트랜잭션을 시작해야 하는 경우에 사용할 수 있다. 만약 이미 시작된 트랜잭션이 있으면 트랜잭션을 잠시 보류시킨다. 만약 JTA 트랜잭션 매니저를 사용한다면 서버의 트랜잭션 매니저에 트랜잭션 보류가 가능하도록 설정되어 있어야 한다.
- **NOT_SUPPORTED**
    - 이미 진행중인 트랜잭션이 있으면 이를 보류시키고, 트랜잭션을 사용하지 않도록 한다.
- **NEVER**
    - 이미 진행중인 트랜잭션이 있으면 예외를 발생시키며, 트랜잭션을 사용하지 않도록 강제한다.
- **NESTED**
    - NESTED는 이미 진행중인 트랜잭션이 있으면 중첩 트랜잭션을 시작한다. 중첩 트랜잭션은 트랜잭션 안에 다시 트랜잭션을 만드는 것으로, 독립적인 트랜잭션을 만드는 REQUIRES_NEW와는 다르다. NESTED에 의한 중첩 트랜잭션은 먼저 시작된 부모 트랜잭션의 커밋과 롤백에는 영향을 받지만, 자신의 커밋과 롤백은 부모 트랜잭션에게 영향을 주지 않는다.
    - 예를 들어 어떤 중요한 작업을 진행하면서 작업 로그를 DB에 저장해야 한다고 해보자. 그런데 로그를 저장하는 작업은 실패를 하더라도 메인 작업의 트랜잭션까지는 롤백하지 말아야 하는 경우가 있다. 왜냐하면 힘들게 처리한 중요한 작업을 로그를 남기지 못해서 모두 실패로 만들 수 없기 때문이다. 반면에 핵심 작업에서 예외가 발생한다면 이때는 저장된 로그도 제거해야 한다.
    - 이렇듯 부모의 트랜잭션은 자식의 작업에 영향을 줘야하지만 자식의 트랜잭션은 부모에 영향을 주지 않아야 할 때 NESTED 전파 속성을 이용할 수 있다.

## 2. 트랜잭션 격리수준: isolation

트랜잭션 격리수준은 동시에 여러 트랜잭션이 진행될 때 트랜잭션의 작업 결과를 다른 트랜잭션에게 어떻게 노출할 것인지를 결정한다. 

스프링은 다음의 5가지 격리수준 속성을 지원한다.

- **DEFAULT**
    - DEFAULT는 사용하는데이터 액세스 기술 또는 DB 드라이버의 디폴트 설정을 따른다. 물론 일반적으로 드라이버의 격리 수준은 DB의 격리 수준을 따르며, 오라클은 READ_COMMITED, MySQL은 REPEATABLE_READ를 기본 격리수준으로 갖는다.

- **READ_UNCOMMITTED**
    - READ_UNCOMMITTED는 가장 낮은 격리수준으로써 하나의 트랜잭션이 커밋되기 전에 그 변화가 다른 트랜잭션에 그대로 노출되는 문제가 있다. 하지만 가장 빠르기 때문에 데이터의 일관성이 조금 떨어지더라도 성능을 극대화할 때 의도적으로 사용한다.

- **READ_COMMITTED**
    - READ_COMMITTED는 READ_UNCOMMITTED와 달리 다른 트랜잭션이 커밋하지 않은 정보는 읽을 수 없다. 대신 하나의 트랜잭션이 읽은 로우를 다른 트랜잭션이 수정할 수 있다. 이 때문에 처음 트랜잭션이 같은 로우를 다시 읽을 때 다른 내용이 발견될 수 있다.

- **REPEATABLE_READ**
    - REPEATABLE_READ는 하나의 트랜잭션이 읽은 로우를 다른 트랜잭션이 수정할 수 없도록 막아준다. 하지만 새로운 로우를 추가하는 것은 막지 않는다. 따라서 SELECT로 조건에 맞는 로우를 전부 가져오는 경우 트랜잭션이 끝나기 전에 추가된 로우가 발견될 수 있다.

- **SERIALIZABLE**
    - SERIALIZABLE은 가장 강력한 트랜잭션 격리 수준으로, 이름 그대로 트랜잭션을 순차적으로 진행시켜준다. 그렇기 때문에 SERIALIZABLE은 여러 트랜잭션이 동시에 같은 테이블의 정보를 액세스할 수 없다.
    - SERIALIZABLE은 가장 안전하지만 가장 성능이 떨어지는 격리수준이기 때문에 극단적으로 안전한 작업이 필요한 경우가 아니라면 사용해서는 안된다.

## 3. 트랜잭션 제한시간: timeout

timeout을 이용하면 트랜잭션에 제한시간을 지정할 수 있다. 값은 int 타입의 초 단위로 지정할 수 있는데 문자열로 지정하기를 원한다면 timeoutString을 사용하면 된다.

만약 별도로 값을 설정해주지 않는다면 트랜잭션 시스템의 제한시간을 따르며, 제한 시간을 직접 지정했는데 트랜잭션 매니저에서 이 기능을 지원하지 못한다면 예외가 발생할 수 있다.

## 4. 읽기전용 트랜잭션: read-only, readOnly

Spring에서는 트랜잭션을 다음의 2가지 목적으로 읽기 전용으로 설정할 수 있다.

- 읽기 전용으로 설정함으로써 성능을 최적화함
- 쓰기 작업이 일어나는 것을 의도적으로 방지함

### **ReadOnly 성능 이점**

- JPA를 사용할 경우 `readOnly=true` 옵션을 주면 영속성 컨텍스트를 플러시가 일어나지 않아 쓰기, 수정, 삭제 작업이 동작하지 않는다. 또한, 스냅샷 저장, 비교 등의 무거운 작업을 하지 않아 성능이 향상된다.
- 데이터베이스가 master와 slave로 나누어져 있다면 `readOnly=true` 옵션을 주면 읽기 전용인 slave를 호출하기 된다. 즉, 상황에 따라 데이터베이스 서버 부하를 줄일 수 있다.

### **DBMS 별 ReadOnly 동작방식**

- Oracle
    - ReadOnly 트랜잭션을 이용할 경우 이 트랜잭션이 시작되기 이전에 커밋된 데이터만 접근할 수 있으며, 트랜잭션 실행되는 동안 커밋되는 데이터는 결과에 반영되지 않는다.
    - ReadOnly 트랜잭션을 이용할 경우 지원하는 DML은 SELECT 구문뿐이다.
- MySQL
    - ReadOnly 트랜잭션을 이용할 경우 트랜잭션 ID 설정에 대한 오버헤드를 해결할 수 있다(트랜잭션 ID가 부여되지 않는다). —> 좀 더 알아봐야겠습니다..

## 5. 트랜잭션 롤백/커밋 예외

`@Transactional`에서 런타임 예외가 발생하면 롤백하고, 예외가 발생하지 않았거나 체크 예외가 발생하였다면 커밋한다. 

여기서 체크 예외를 커밋 대상으로 삼은 이유는 체크 예외가 예외적인 상황에서 사용되기 보다는 반환값을 대신해 비지니스적인 의미를 담은 결과로 많이 사용되기 때문이다. 반대로 스프링에서 데이터 액세스 기술의 예외는 런타임 예외로 전환하여 던지므로 런타임 예외만 롤백 대상으로 삼은 것이다.

롤백/커밋의 동작 방식의 변경을 원한다면 설정을 통해 동작 방식을 바꿀 수 있다. 

- 커밋 대상이지만 롤백을 발생시킬 예외나 클래스 이름
    - `rollbackFor`
    - `rollbackForClassName`
- 롤백 대상인 런타임 예외를 트랜잭션 커밋 대상
    - `noRollbackFor`
    - `noRollbackForClassName`

# 참고

- 토비의 스프링 3.1
- [https://mangkyu.tistory.com/169](https://mangkyu.tistory.com/169)
- [https://tecoble.techcourse.co.kr/post/2021-05-25-transactional/](https://tecoble.techcourse.co.kr/post/2021-05-25-transactional/)
- [https://goddaehee.tistory.com/167](https://goddaehee.tistory.com/167)
- [https://velog.io/@kdhyo/JavaTransactional-Annotation-알고-쓰자-26her30h](https://velog.io/@kdhyo/JavaTransactional-Annotation-%EC%95%8C%EA%B3%A0-%EC%93%B0%EC%9E%90-26her30h)
- [https://lob-dev.tistory.com/entry/DBMS-별-Transaction-Read-Only에-대한-동작-방식-1](https://lob-dev.tistory.com/entry/DBMS-%EB%B3%84-Transaction-Read-Only%EC%97%90-%EB%8C%80%ED%95%9C-%EB%8F%99%EC%9E%91-%EB%B0%A9%EC%8B%9D-1)
