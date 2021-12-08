# 목표

Sync-Blocking, Async-NonBlocking 뿐만 아니라 Sync-NonBlocking과 Async-Blocking 개념을 이해한다
||Blocking|Nonblocking|
|-|-|-|
|**Synchronous**|Sync-Blocking|Sync-Nonblocking|
|**Asynchronous**|Async-Blocking|Async-Nonblocking|

위 4개 분면을 이해할 수 있게 되는 것이다!

# 기본 개념

Synchronous와 Blocking은 무언가를 기다리게 하고,
Asynchronous와 Nonblocking은 기다리지 않고 바로바로 처리된다

## Synchronous & Asynchronous

한 작업이 다른 작업을 호출하는 경우, 호출되는 함수의 작업 완료 여부를 누가 신경쓰는지에 따라 나뉜다

"신경쓴다"는 방법으로는 끝났으면 끝났다는 말을 들을 때까지 기다리기, 그리고 끝났는지 계속 물어보기, 2가지 방식이 있다

- **Asynchronous** : 호출하는 함수가 작업 완료 여부를 신경쓰지 않는다  
→ 호출되는 함수에게 Callback을 전달해서 작업을 완료하면 실행하도록 한다  
→ 기다리지도, 물어보지도 않는다  
Asynchronous를 구현하기 위해 **호출된 함수의 작업은 별도의 thread로 빼서 실행**하며 완료된 호출한 함수에게 알려준다  
- **Synchronous** : 호출하는 함수가 작업 완료 여부를 계속 확인한다  
→ 호출하는 함수는 호출괴는 함수의 작업 완료 여부 또는 작업 완료 후 리턴을 기다리고 있다  
→ 기다리거나 물어본다  

## Blocking & Nonblocking

한 작업이 다른 작업을 호출하는 경우, 호출되는 함수가 바로 리턴을 하는지 여부에 따라 나뉜다

누구한테 제어권(작업을 할 권리)이 있는가!

- **Nonblocking** : 호출된 함수가 바로 리턴한다  
→ 호출된 함수가 제어권을 바로 호출한 함수에게 넘겨주어 다른 일을 할 수 있도록 한다  
→ 제어권은 호출한 함수에게 있다  
- **Blocking** : 호출된 함수가 자신의 작업을 완료할때까지 리턴하지 않는다  
→ 호출된 함수는 자신의 작업을 완료하면 호출한 함수에게 제어권을 넘겨주므로 호출한 함수는 다른 일을 하지 않고 대기한다  
→ 제어권은 호출된 함수에게 있다  

# Sync-Blocking & Async-Nonblocking

![https://i.imgur.com/iSafBIF.png](https://i.imgur.com/iSafBIF.png)

## Sync-Blocking

Synchronous + Blocking  
⇒ 호출하는 함수는 호출된 함수의 작업 완료/리턴을 계속 기다린다  
⇒ 호출된 함수는 자신의 작업이 완료되면 리턴한다  

따라서 호출한 함수는 함수를 호출한 후 리턴을 받기 전까지 다른 일을 하지 않고 대기한다

## Async-Nonblocking

Asynchronous + Nonblocking  
⇒ 호출하는 함수는 호출된 함수의 작업 완료/리턴을 기다리지 않는다  
⇒ 호출된 함수는 호출되면 바로 리턴한다  

따라서 호출한 함수는 호출 이후 바로 제어권을 넘겨받아 다른 일을 한다  

# Sync-Nonblocking

Synchronous + Nonblocking  
⇒ 호출하는 함수는 호출된 함수의 작업 완료/리턴을 계속 기다린다  
⇒ 호출된 함수는 호출되면 바로 리턴한다  

따라서 호출한 함수는 호출 이후 제어권을 바로 받기 때문에 작업을 계속 하면서, 호출했던 함수의 작업완료는 계속해서 확인하게 된다

![https://i.imgur.com/a8xZ9No.png](https://i.imgur.com/a8xZ9No.png)

# Async-Blocking

Asynchronous + Blocking  
⇒ 호출하는 함수는 호출된 함수의 작업 완료/리턴을 기다리지 않는다  
⇒ 호출된 함수는 자신의 작업이 완료되면 리턴한다  

호출한 함수는 호출한 함수의 리턴을 기다리지는 않지만, 호출된 함수는 자신의 작업이 완료될 때까지 제어권을 넘겨주지 않기 때문에 호출된 함수의 작업이 완료될 때까지 대기하게 된다

즉, Sync-Blocking과 유사하게 동작하며, 이 방식에 비해 별다른 이점은 없다

![https://i.imgur.com/zKF0CgK.png](https://i.imgur.com/zKF0CgK.png)

그러나 **Aysnc-Nonblocking을 추구하다 의도치 않게 Async-Blocking이 되는 경우**가 있다
다시말해, 호출한 함수가 호출된 함수를 신경쓰지 않고 다른 일을 하게 하도록 하고 싶은데, 어쩔 수 없이 호출된 함수를 기다리게 되는 경우다

대표적인 예시는 Node.js 와 MySQL의 조합이다
Node.js에서 Callback 함수를 통해 Asynchronous 방식을 택하여도 DB 작업 호출 시 MySQL 드라이버를 호출하게 되는데 이 드라이버가 Blocking 방식이기 때문이다

# 정리

![https://camo.githubusercontent.com/b7b28ae739c50d5ed8a4594f52f24e671aeeae234befd0991e5230561ba303bf/68747470733a2f2f696d67312e6461756d63646e2e6e65742f7468756d622f523132383078302f3f73636f64653d6d746973746f72793226666e616d653d6874747073253341253246253246626c6f672e6b616b616f63646e2e6e6574253246646e25324664613530597a2532466274713044736a65345a562532466c47653848386e5a676442646746766f3749637a5330253246696d672e706e67](https://camo.githubusercontent.com/b7b28ae739c50d5ed8a4594f52f24e671aeeae234befd0991e5230561ba303bf/68747470733a2f2f696d67312e6461756d63646e2e6e65742f7468756d622f523132383078302f3f73636f64653d6d746973746f72793226666e616d653d6874747073253341253246253246626c6f672e6b616b616f63646e2e6e6574253246646e25324664613530597a2532466274713044736a65345a562532466c47653848386e5a676442646746766f3749637a5330253246696d672e706e67)

# I/O 작업

입출력 작업은 커널 레벨에서만 수행할 수 있으므로 프로세스 및 스레드는 커널에게 I/O를 요청해야 한다
즉, 프로세스 및 스레드는 "호출하는 함수", I/O 작업은 "호출되는 함수"가 된다

## **I/O Blocking**

1. 프로세스/스레드가 커널에게 I/O를 요청하는 함수(recvfrom) 호출
2. 커널 작업 완료
3. 작업 결과 리턴

![https://t1.daumcdn.net/cfile/tistory/2371EC4955160B8714](https://t1.daumcdn.net/cfile/tistory/2371EC4955160B8714)

→ 커널 작업 중에 프로세스/스레드는 **자신의 작업을 중단하고 대기한다**

→ 커널 작업 중 CPU 자원을 쓰지 않으므로 **자원 낭비가 심하다**

→ 여러 클라이언트가 접속하는 서버인 경우, I/O 작업을 호출한 작업은 중지되어도 다른 클라이언트가 실행하는 작업은 중지되면 안되기 때문에 클라이언트 별로 스레드를 생성하게 되어 **클라이언트의 수가 매우 많아진다**

## **I/O Nonblocking**

1. 프로세스/스레드가 커널에게 I/O를 요청하는 함수(recvfrom) 호출
2. 커널은 곧바로 리턴
3. 커널 작업이 완료되면 데이터를 리턴함

![https://t1.daumcdn.net/cfile/tistory/253D9E475516150118](https://t1.daumcdn.net/cfile/tistory/253D9E475516150118)

→ 커널 작업 중에 프로세스/스레드는 자신의 작업을 진행한다

# 비고
## 결론
### Synchronous & Asynchronous 의 차이
Synchronous와 Asynchronous의 차이는 함수가 다른 함수를 호출한 이후, 호출된 함수의 작업 완료 여부에 신경을 쓰는지의 여부에 있습니다.
Synchronous의 경우 다른 함수를 호출한 이후에 해당 함수가 작업을 완료했을 때 리턴을 받거나 작업을 완료했는지 지속적으로 확인하는 방식을 통해 신경씁니다.
이에 반해 Asynchronous의 경우 호출한 함수는 호출된 함수의 작업 완료 여부에 대해 확인받거나 물어보는 과정없이 신경쓰지 않습니다.
이를 구현하기 위해 호출된 함수는 별도의 thread를 생성하여 자신의 작업을 실행하며, 완료 시 callback 함수를 통해 완료 여부를 알려주게 됩니다.

### Blocking & Nonblocking 의 차이
Blocking과 Nonblocking의 차이는 다른 함수에 의해 호출된 함수가 즉시 리턴을 하는지 여부에 있습니다.
Blocking의 경우 호출된 함수는 자신의 작업이 완료될 때 까지 호출한 함수에 리턴하지 않습니다.
제어권을 자신이 가지고 있기 때문에 호출한 함수는 호출된 함수가 작업을 완료하고 리턴할 때 까지 대기하게 됩니다.
반면에 Nonblocking의 경우 호출된 함수는 거의 즉시 자신을 호출한 함수에 리턴합니다.
제어권을 호출한 함수에 돌려주는 것이기 때문에 호출한 함수는 자신의 작업을 이어갈 수 있습니다.
즉, 이 둘의 가장 큰 차이는 호출한 함수가 자신의 작업을 계속 할 수 있는지 대기해야하는지 여부라고 할 수 있습니다.

### "Synchronous & Asynchronous" 와 "Blocking & Nonblocking" 의 차이
Synchronous와 Asynchronous는 호출된 함수의 작업 완료 여부를 호출한 함수가 신경을 쓰는지를 구분하는 방식이라면,
Blocking과 Nonblocking은 호출된 함수가 호출 즉시 리턴을 하는지 여부에 따라 구분하는 방식입니다.
구현 방식으로 비교해보면, Asynchronous는 별도의 thread를 생성하여 호출된 함수를 실행하는 방식으로 구현하며, Nonblocking는 호출된 함수 자체의 제어문에서 지체없이 리턴하는 방식으로 구현합니다.

## 출처
- [http://homoefficio.github.io/2017/02/19/Blocking-NonBlocking-Synchronous-Asynchronous/](http://homoefficio.github.io/2017/02/19/Blocking-NonBlocking-Synchronous-Asynchronous/)
- [https://github.com/gyoogle/tech-interview-for-developer/blob/master/Computer%20Science/Network/%5BNetwork%5D%20Blocking%20Non-Blocking%20IO.md](https://github.com/gyoogle/tech-interview-for-developer/blob/master/Computer%20Science/Network/%5BNetwork%5D%20Blocking%20Non-Blocking%20IO.md)
- [https://ozt88.tistory.com/20](https://ozt88.tistory.com/20)
