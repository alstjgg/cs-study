## 1. DB Connection
- DB를 사용하기 위해 DB와 어플리케이션 간 통신을 할 수 있는 수단
- DB Connection은 Database Driver와 Database 연결정보를 담은 URL이 필요하다.
- Java의 DB Connection은 JDBC를 주로 이용하는데, URL타입을 사용한다.

 *2Tier - 클라이언트로서의 자바 프로그램(애플릿/JSP/Application)이 직접 데이터베이스 서버로 접근하여 데이터를 엑세스하는 구조
 
 *3Tier - 자바 프로그램과 데이터베이스 서버 중간에 미들웨어 층을 두어, 그 미들웨어층에게 비즈니스 로직구현부터 트랜잭션 처리, 리소스 관리 등을 전부 맡기는 구조. 일반적으로 미들웨어는 하나 이상으로 구성되며, 통상 3Tier로 칭함.
![11](https://user-images.githubusercontent.com/48278519/147056416-700778f1-5533-4d5f-b58d-bd4ea585fac2.jpg)


## 2. JDBC
- Java Database Connectivity 의 약어로 자바 언어로 다양한 종류의 관계형 데이터베이스에 접속하고 SQL 문을 수행하여 처리하고자할때 사용되는 표준 SQL 인터페이스 API 이다.
원래라면 DB마다 연결방식과 통신 규격이 따로 있기에 프로그램을 DB와 연결한다면 해당 DB와 관련된 기술적 내용을 배우고, DB가 변경될 시 많은 변경사항이 존재해야 한다. 하지만 각 DBMS 에 맞는 JDBC 를 받아주게되면 쉽게 DBMS 를 변경할 수 있게 된다. 
- **즉, DBMS 종류(MySql, MsSql, Oracle...)에 상관 없이 하나의 JDBC API를 사용해서 데이터베이스 작업을 처리할수 있게 된다.** JDBC API를 사용하는 어플리케이션의 개략적인 구조는 아래와 같다.
![22](https://user-images.githubusercontent.com/48278519/147056453-fdfedc31-ee31-420b-82cc-20fc3e5a454d.jpg)
- 자바(웹)어플리케이션에서 데이터베이스 에 접근하기위해선 JDBC API를 이용해서 데이터베이스에 접근하고,  JDBC API는 JDBC 드라이버를 거쳐 데이터베이스와 통신을 한다.

**JDBC 드라이버**
- 자바 프로그램의 요청을 DBMS가 이해할 수 있는 프로토콜로 변환해주는 클라이언트 사이드 어댑터
- 각각의 DBMS는 자신에게 알맞은 JDBC드라이버를 제공하고 있음
 
### JDBC 실행 과정
![3](https://user-images.githubusercontent.com/48278519/147056919-d8006b55-f193-4f59-88fe-02074ae190e2.png)

```
1. DB벤더에 맞는 드라이버 로드
2. DB서버의 IP, ID, PW등을 DriverManager 클래스의 getConnection 메소드를 사용하여 Connection 객체 생성
3. Connection으로 부터 PreparedStatement 객체를 받음
4. executeQurey를 수행하고 ResultSet객체를 받아 데이터를 처리
5. 사용했던 ResultSet, PreparedStatement, Connection을 close
```

**getConnection()**
- DriverManager.getConnection()은 실제 자바프로그램과 데이터베이스를 네트워크상에서 연결을 해주는 메소드이다.
- 연결에 성공하면 DB 와 연결 상태를 Connection 객체로 표현하여 반환하고, 가장 부하가 많이 걸리는 과정이다. 


**Connection**
- DB 연결 객체
- 데이터베이스로의 연결 기능을 제공하며, Statement 객체를 생성하는 기능 제공
- SQL문을 데이터베이스에 전송하거나, 이러한 SQL문을 커밋하거나 롤백하는데 사용
- 보통 Connection 하나당 트랜잭션 하나를 관리한다.Mybatis 의 SqlSession, Hibernate 에 TransactionManager 등의 Close 가 이루어지면 Connection 을 ConnectionPool 에 반납하게 된다.


**Statement 또는 PreparedStatment**
 - SQL 문 실행 객체

**ResultSet**
 - 쿼리문의 결과를 가지는 객체

=> 객체들은 DB관련 처리작업이 완료된 다음에는 반드시 사용했던 객체들을 메모리에서 해제(close())를 해줘야 함

**Driver** 
- 각 JDBC Driver벤더에서 구현해야 하는 인터페이스

**DriverManager** 
- 사용될 드라이버를 등록하거나 등록을 해제하는 기능과 DB로의 연결 기능

## 3. Connection Pool
(JDBC 예제 소스)
```java
Connection conn = null;
PreparedStatement  pstmt = null;
ResultSet rs = null;

try {
    sql = "SELECT * FROM T_BOARD"

    // 1. 드라이버 연결 DB 커넥션 객체를 얻음
    connection = DriverManager.getConnection(DBURL, DBUSER, DBPASSWORD);

    // 2. 쿼리 수행을 위한 PreparedStatement 객체 생성
    pstmt = conn.createStatement();

    // 3. executeQuery: 쿼리 실행 후
    // ResultSet: DB 레코드 ResultSet에 객체에 담김
    rs = pstmt.executeQuery(sql);
    } catch (Exception e) {
    } finally {
        conn.close();
        pstmt.close();
        rs.close();
    }
}
```
위와 같이 자바에서 DB에 직접 연결해서 처리하는 경우(JDBC) 드라이버(Driver)를 로드하고 커넥션(connection) 객체를 받아와야 한다. 그러면 매번 사용자가 요청을 할 때마다 드라이버를 로드하고 커넥션 객체를 생성하여 연결하고 종료하기 때문에 매우 비효율적이다. 이런 문제를 해결하기 위해서 커넥션풀(DBCP)를 사용한다.


<br>

### 1) 커넥션 풀(Connection Pool)
- 웹 컨테이너(WAS)가 실행되면서 일정량의 Connection객체(DB 연결 객체)를 미리 만들어어서 pool에 저장했다가, 클라이언트 요청이 오면 Connection객체를 빌려주고, 해당 객체의 임무가 완료되었으면 다시 반납 받아서 pool에 저장을 하는 프로그래밍 기법

- Container 구동 시 일정수의 Connection 객체를 생성하게 되며 클라이언트의 요청에 의해 Application 이 DBMS 작업을 수행해야 하면 Connection Pool 에서 Connection 객체를 받아와 작업을 진행하며 작업이 끝나면 다시 Connection Pool 에 반납하는 과정을 거친다.
![4](https://user-images.githubusercontent.com/48278519/147059716-43366b9e-ee82-4ef1-8056-8595656920b2.png)



### 2) 동작 원리 
 -  Hikari CP가 동작하는 방식
 ![5](https://user-images.githubusercontent.com/48278519/147059733-79b1d7b5-3104-433c-b7ed-197e96d6bf90.png)

 1. Thread가 Connection을 요청하면 Connection Pool의 각자의 방식에 따라 유휴 Connection을 찾아서 반환한다. Hikari CP의 경우, 이전에 사용했던 Connection이 존재하는지 확인하고, 이를 우선적으로 반환하는 특징을 가짐


![6](https://user-images.githubusercontent.com/48278519/147059744-f02d28f2-63b8-4c53-bd29-c172b7dbe64b.png)

 2. 가능한 Connection이 존재하지 않으면, HandOffQueue를 Polling 하면서 다른 Thread가 Connection을 반납하기를 기다린다. (지정한 TimeOut 시간까지 대기하다가 시간이 만료되면 Exception을 던짐)


![7](https://user-images.githubusercontent.com/48278519/147059751-7c527c72-bae9-426c-8d28-5a7c587747c1.png)

 3. 최종적으로 사용한 Connection을 반납하면 Connection Pool이 사용내역을 기록하고, HandOffQueue에 반납된 Connection을 삽입. 이를 통해 HandOffQueue를 Polling 하던 Thread는 Connection을 획득하고 작업을 이어나간다. 

<br>

### 3) 커넥션 풀(DBCP)의 장점 
- DB접속 설정 객체를 미리 만들어 연결하여 메모리 상에 등록해 놓기 때문에 불필요한 작업(커넥션 생성,삭제)이 사라지므로 클라이언트가 빠르게 DB에 접속 가능 
- DB Connection 수를 제한 할 수 있어서 과도한 접속으로 인한 서버 자원 고갈 방지 가능
- DB 접속 모듈을 공통화해 DB서버의 환경이 바뀔 경우 쉬운 유지보수 가능
- 연결이 끝난 Connection을 재사용함으로써 새로 객체를 만드는 비용을 줄일 수 있음

<br>

## 4. 커넥션 풀(DBCP) 유의 사항
### 1) 동시 접속자가 많을 경우
너무 많은 DB접근이 발생할 경우에는 커넥션(Connection)은 한정되었기 때문에 쓸수 있는 커넥션이 반납될때 까지 기다려야 한다. 
<br>
너무 많은 커넥션(Connection)을 생성할시엔 Connection 또한 객체이므로 많은 메모리를 차지하게 되고, 프로그램의 성능을 저하시키는 원인이 될 수 있다.
<br>
```즉, WAS에서 커넥션 풀을 크게 설정하면 메모리 소모가 큰 대신 많은 사용자가 대기시간이 줄어들고, 반대로 커넥션 풀을 적게 설정하면 그 만큼 대기시간이 길어진다.``` 따라서 사용량에 따라 적정량의 커넥션(Connection)객체를 생성해두어야 한다.


### 2) Connection Pool이 커지면 성능은 무조건 좋아질까?
아님. Connection의 주체는 Thread이므로 Thread와 함께 고려해야 함 

**1) Thread Pool 크기 < Connection Pool 크기 인 경우**
  - Thread Pool에서 트랜잭션을 처리하는 Thread가 사용하는 Connection 외에 남는 Connection은 실질적으로 메모리 공간만 차지하게 됨 (메모리 낭비)

**2) Thread도 늘리고 Connection Pool도 증가시키는 경우**
  - Thread의 증가로 인해 더 많은 Context Switching이 발생
  - Disk 경합 측면에서 성능 한계 발생 : 데이터베이스는 하드 디스크 하나당 하나의 I/O를 처리하기 때문에 Blocking이 발생한다. 즉, 특정 시점부터는 성능적인 증가가 Disk 병목으로 인해 미비해짐

```=> 데이터베이스 입장에서 Connection은 Thread와 어느 정도 일치한다고 볼 수 있다.  Connection이 많다는 의미는 데이터베이스 서버가 Thread를 많이 사용한다는 것을 의미하고, 이에 따라 Context Switching으로 인한 오버헤드가 더 많이 발생하기 때문에 Connection Pool을 아무리 늘리더라도 성능적인 한계가 존재```

<br>

### 3) Connection Pool의 크기는 얼마가 적절할까?

```1connections = ((core_count * 2) + effective_spindle_count)```

*by)Hikari CP의 공식문서 : https://github.com/brettwooldridge/HikariCP/wiki/About-Pool-Sizing

![8](https://user-images.githubusercontent.com/48278519/147061077-04b3f4fa-5068-47ee-951c-fb6befe291e2.png)


**core_count: 현재 사용하는 서버 환경에서의 CPU 개수 **
- core_count * 2를 하는 이유는 Context Switching 및 Disk I/O 와 관련이 있다.
- Context Switching으로 인한 오버헤드를 고려하더라도 데이터베이스에서 Disk I/O (혹은 DRAM이 처리하는 속도)보다 CPU 속도가 월등히 빠르다. 그렇기 때문에 Thread가 Disk와 같은 작업에서 Blocking 되는 시간에 다른 Thread의 작업을 처리할 수 있는 여유가 생기고, 여유 정도에 따라 멀티 스레드 작업을 수행할 수 있게 된다. Hikari CP가 제시한 공식에서는 계수를 2로 선정하여 Thread 개수를 지정했다. 

**effective_spindle_count : 기본적으로 DB서버가 관리할 수 있는 동시 I/O 요청 수**
- 하드 디스크 하나는 spindle 하나를 가진다. 
- 디스크가 16 개가 있는 경우 시스템은 동시에 16 개의 I/O 요청을 처리할 수 있다.(디스크 RAID 구성 방식에 따라서 달라질 수는 있음) 


## 5. 커넥션 풀(DBCP) 종류

### 1) commons-dbcp
- https://commons.apache.org/proper/commons-dbcp/
- 아파치에서 제공해주는 대표적 Connecrion Pool 라이브러리


|속성명|설명|
|---|---|
|**initialSize**| BasicDataSource 클래스 생성 후 최초로 getConnection() 메서드를 호출할 때 커넥션 풀에 채워 넣을 커넥션 개수|
|**maxActive**| 동시에 사용할 수 있는 최대 커넥션 개수(기본값: 8)|
|**maxIdle**| 커넥션 풀에 반납할 때 최대로 유지될 수 있는 커넥션 개수(기본값: 8)|
|**minIdle** | 최소한으로 유지할 커넥션 개수(기본값: 0)|


![9](https://user-images.githubusercontent.com/48278519/147061683-ee390fb0-49c1-42ce-8b4d-e9e38bee1084.png)

위의 예시에선 8개의 커넥션을 최대로 활용할 수 있는 상태이며, 4개는 사용 중이고 4개는 대기 중인 상태

**maxActive >= initialSize**
- 최대 커넥션 개수는 초기에 생성할 커넥션 개수와 같거나 크게 설정해야 한다.

**maxActive = maxIdle** 
- maxActive 값과 maxidle 값은 같은 것이 바람직하다. 만약 둘의 값이 아래와 같다고 가정해보자.

```maxActive = 10 maxIdle = 5```

항상 커넥션을 동시에 5개는 사용하고 있는 상황에서 1개의 커넥션이 추가로 요청된다면 maxActive = 10 이므로 1개의 추가 커넥션을 데이터베이스에 연결한 후 Pool은 비즈니스 로직으로 커넥션을 전달 한다. 이후 비즈니스 로직이 커넥션을 사용 후 풀에 반납할 경우, maxIdle = 5 에 영향을 받아 커넥션 을 실제로 닫아버리므로, 일부 커넥션을 매번 생성했다 닫는 비용이 발생할 수 있다.

커넥션 개수와 관련된 가장 중요한 성능 요소는 일반적으로 커넥션의 최대 개수다. 4개 항목의 설정 값 차이는 성능을 좌우하는 중요 변수는 아니다.

maxActive 값은 DBMS의 설정과 애플리케이션 서버의 개수, Apache, Tomcat에서 동시에 처리할 수 있는 사용자 수 등을 고려해서 설정해야 한다. DBMS가 수용할 수 있는 커넥션 개수를 확인한 후에 애플리케이션 서버 인스턴스 1개가 사용하기에 적절한 개수를 설정한다. 사용자가 몰려서 커넥션을 많이 사용할 때는 maxActive 값이 충분히 크지 않다면 병목 지점이 될 수 있다. 반대로 사용자가 적어서 사용 중인 커넥션이 많지 않은 시스템에서는 maxActive 값을 지나치게 작게 설정하지 않는 한 성능에 큰 영향이 없다.

Commons DBCP에서는 DBMS에 로그인을 시도하고 있는 커넥션도 사용 중인 것으로 간주한다. 만약 DBMS에 로그인을 시도하고 있는 상태에서 무한으로 대기하고 있다면, 애플리케이션에서 모든 커넥션이 사용 중인 상태가 돼 새로운 요청을 처리하지 못할 수도 있다. 이런 경우 장애 확산을 최소화하려면 Microsoft SQL Server의 JDBC 드라이버에서 설정하는 loginTimeOut 속성같은 JDBC 드라이버별 타임아웃 속성을 설정하는 것이 좋다.



### 2) tomcat-jdbc-pool
- https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html
- tomcat에 내장되어 사용되고 있다
- Apache Commons DBCP 라이브러리를 바탕으로 만들어짐
- Spring boot 2.0.0 하위 버전에서 사용하는 기본 DBCP

<br>

### 3) HikariCP
- https://github.com/brettwooldridge/HikariCP
- 스프링부트 2.0부터 default JDBC connecrion pool임
- zero-overhead의 특징을 가짐

*overhead : 어떤 처리를 하기 위해 들어가는 간접저긴 처리시간 메모리


![10](https://user-images.githubusercontent.com/48278519/147064144-9567af5d-c897-48d6-9419-ba57ad2ef117.png)

Springboot 환경에서는 application.properties에서 간단하게 HikariCP의 설정을 할 수 있다. 

![1212](https://user-images.githubusercontent.com/48278519/147064160-3bd0834b-cb69-493f-97ff-cfe81d0aa9df.png)

위와 같은 설정을 application.properties에서 해줄 수 있다. 원하는 HikariCP의 연결정보 뿐만 아니라 원하는 Connection Pool의 크기, 시간 등등을 설정해줄 수 있다.

 
 
## 6. 요약
- JDBC는 자바 어플리케이션이 데이터베이스에 접근할수 있도록 만든 JAVA에서 제공하는 API입니다.
- 하나의 JDBC로 어떤 DBMS든 각회삭 제공하는 JDBC드라이버를 통해 연결 할 수 있습니다.
- 커넥션 풀이란 JDBC 실행과정중에서 생성되어야할 Connection 객체를 미리만들어서 pool 이란 곳에서 저장을 해두는 기법입니다.
- 장점은 불필요한 과정(Connection객체를 생성,삭제)을 줄여서 성능을 향상시킬수 있습니다.
- WAS에서 커넥션 풀을 크게 설정하면 메모리 소모가 큰 대신 많은 사용자가 대기시간이 줄어들고, 반대로 커넥션 풀을 적게 설정하면 그 만큼 대기시간이 길어집니다. 따라서 사용량에 따라 적정량의 커넥션(Connection)객체를 생성해두어야 한다.



## 7. 도움이 될만한 블로그 

**connection pool을 사용해야 하는 이유**

https://devkly.com/db/db-connection-pool/

**JDBC 관련 튜닝**

https://jiku90.tistory.com/14


**리소스 관리 방식 이해하기**

https://kakaocommerce.tistory.com/45

**질문 목록 **

https://aspdotnet.tistory.com/2309

## 8.출처

https://minkwon4.tistory.com/168
https://kimvampa.tistory.com/44
https://wonyong-jang.github.io/spring/2020/03/17/Spring-Connection-Pool-DBCP.html
https://velog.io/@hyejinjeong9999/%EC%BB%A4%EB%84%A5%EC%85%98-%ED%92%80
https://linked2ev.github.io/spring/2019/08/14/Spring-3-%EC%BB%A4%EB%84%A5%EC%85%98-%ED%92%80%EC%9D%B4%EB%9E%80/
https://blog.naver.com/PostView.nhn?isHttpsRedirect=true&blogId=ssang8417&logNo=221858327113&categoryNo=24&parentCategoryNo=0&viewDate=&currentPage=1&postListTopCurrentPage=1&from=postView
https://hyuntaeknote.tistory.com/12
