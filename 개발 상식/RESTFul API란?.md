## RESTFul API 한줄 요약
#### REST 아키텍쳐의 제약조건을 준수하는 API
#### REST API 설계 가이드에 따라 API를 만들어서 웹 서비스를 제공하면 해당 웹 서비스는 RESTFul하다고 말합니다.

<br>

## REST(REpresentational State Transfer)
웹상에서 정보들을 주고받을 때 널리 쓰이는 규격화된 통신 규칙을 의미합니다.<br>
좀 더 자세히 풀어 말하자면, "URI"를 통해 웹상에서 사용되는 자원을 표현하고, "HTTP Method"를 통해 자원에 대한 CRUD 요청을 정의하는 방식입니다. 
기존 온라인 데이터 전송 방식인 SOAP 프로토콜의 한계점을 극복하기 위해 등장했다고 합니다.
<br>

### REST의 특징
1) HTTP를 활용합니다. 웹의 기존 기술과 HTTP 프로토콜을 그대로 활용하기 때문에 웹의 장점을 최대한 활용할 수 있는 아키텍쳐 스타일입니다.(HTTP 사용의 모범 사례)
2) 각 요청이 어떤 동작이나 정보에 의한 것인지 그 모습 자체로 추론이 가능합니다.
<br>

## REST의 구성 요소
>### 1. Resource(자원)
  서버는 유니크한 id를 가지는 리소스를 가지고 있으며, 클라이언트는 리소스에 요청을 보낼 수 있습니다. 이 때, 리소스는 URI를 통해 표현된다는 특징이 있습니다.
리소스의 원형으론 document, collection, store, controller가 있는데 document는 DB에서 row처럼 단일 정보를 포함하는 데이터로 생각하면 쉽고 collection은 도큐먼트의 집합을 의미합니다.

  |ID|NAME|CLASS|MAJOR| 
  |---|---|---|---|
  |1|JONGMIN|AClass|ECONOMICS|
  |2|JIHYEOK|BClass|C.E|
  |3|JIHYEON|AClass|MATH|

예를 들어, 도서관 대출 관리 프로그램을 운영한다고 할 때, 위와 같은 학생 정보들이 존재할 것입니다. 이러한 학생 정보를 REST API에선 Resource라고 불립니다. 위 테이블의 전체 정보인 Students를 collection이라고 할 수 있으며, id가 1인 종민 학생의 정보를 document라고 할 수 있습니다.

  <br>

>### 2. Verb(행위)
  리소스를 가공하는 작업들을 REST API에선 Method라고 부르며, HTTP의 Method인 GET/PUT/POST/PATCH/DELETE를 사용합니다. REST API에선 CRUD의 각 연산에 맞는 Method를 사용하여 요청을 보내는 것이 중요합니다. 각 CRUD 연산에서 사용하는 Method는 다음과 같습니다.
  |CRUD연산|--|Method| 
  |---|---|---|
  |Create|--|Post|
  |Read|--|Get|
  |Update|--|Put/Patch|
  |Delete|--|Delete|
  
  <br>
  
  >### 3. Representation of Resource(표현)
   클라이언트가 자원 상태(정보)에 대한 조작을 요청하면 서버는 이에 적절한 응답(representation)을 보내는데, 이 때 클라이언트와 서버가 데이터를 
  주고받는 여러 형태를 의미합니다. 즉, 전달하고자 하는 데이터를 표기하는 방법을 의미하며 json,xml,text 등이 있습니다. 
  
  <br>
  <br>  
  
  ## REST API 설계 가이드
  REST API는 일종의 설계 규약이기 때문에, 어떻게 API를 설계하는지가 REST API에선 중요합니다. REST API의 설계 가이드를 잘 활용해야 RESTFul한 API를 제작할 수 있으며, 이해하기 쉽고 활용도 높은 API를 사용할 수 있을 것입니다.
  <br>
  #### 1. URL에선 명사를 사용한다.
  ```
  GET: /students/Aclass/15/move (X)
  GET: /students/Aclass/15 (O)
  ```
  #### 2. 리소스에 대한 행위(method)는 HTTP Method(POST,GET,PUT,DELETE)로 표현해야 한다.
 
  #### 3. /(슬래시)는 계층 관계를 나타낼 때 사용하며, 마지막에는 항상 엘리먼트(요소, 리소스)가 와야 한다.
  URI에 포함되는 모든 글자는 리소스의 유일한 식별자로 사용되어야 하며, URI의 마지막엔 슬래시로 끝나면 안됩니다.
  
  #### 4. URI에 _ (언더스코어)는 사용하지 않는다.
   가독성을 위해 _ 대신 -(하이픈)을 사용하는 것이 좋습니다.
  
  #### 5. 소문자를 사용한다.
   대소문자에 따라 다른 리소스로 인식하게 되기 때문입니다. RFC 3986(URI 문법 형식)은 URI 스키마와 호스트를 제외하고는 대소문자를 구별하도록 규정합니다.

  #### 6. URI에 파일의 확장자를 포함하지 않는다.
```
   GET / students/Aclass/1/photo.jpg (X)
   GET / students/Aclass/1/photo HTTP/1.1 Host: restapi.example.com Accept: image/jpg  
```
  
  ## 정리
 서두에서 언급한 것처럼, RESTFul API는 REST API의 설계 의도를 모두 만족시킨 API입니다. 
 사실상 REST API의 모든 설계 가이드를 만족시키는 것은 힘들다고 하지만 최대한 RESTFul API에 가까운 API를 설계함으로써 명확하고 구조적인 커뮤니케이션을 이룰 수 있습니다.
   
  ## 참고
  #### 1) API(Application Programming Interface)
  응용 프로그램(애플리케이션)에서 운영 체제나 프로그래밍 언어가 제공하는 기능을 제어할 수 있게 만든 인터페이스. 주로 파일 제어, 창 제어, 화상 처리, 문자 제어 등을 위한 인터페이스를 제공합니다. 

  #### 2) URI 
  URL은 Uniform Resource Locator로 인터넷 상 자원의 위치를 의미합니다. 자원의 위치라는 것은 결국 어떤 파일의 위치를 의미합니다. 반면에 URI는 Uniform Resource Identifier로 인터넷 상의 자원을 식별하기 위한 문자열의 구성으로, URI는 URL을 포함하게 됩니다. 

  #### 3) GET/ POST 차이 
  GET은 웹 브라우저가 서버에 데이터를 요청할 때 사용하고, POST는 웹 브라우저가 서버에 데이터를 전달할 때 사용합니다.<br>
  GET 사용 시 서버로 전달되는 데이터가 인코딩되어 URL에 붙습니다. 따라서 전달되는 데이터가 255개 문자를 초과하면 문제가 발생 할 수 있습니다.<br>
  POST 방식은 전달되는 데이터가 URL에 표시되지 않는다.<br>

  #### 4) PUT/PATCH 차이
  PUT은 자원의 전체 수정이 필요할 때 사용하고, PATCH는 자원의 일부만 수정될 때 사용합니다.

  #### 5) 출처
  - https://velog.io/@somday/RESTful-API-%EC%9D%B4%EB%9E%80
  - https://blog.metafor.kr/165
  - https://mangkyu.tistory.com/46
  - https://velog.io/@taeha7b/api-restapi-restfulapi
  - https://meetup.toast.com/posts/92
