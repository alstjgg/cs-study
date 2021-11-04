# Stack

## 개념

스택은 한 쪽 끝에서만 자료를 넣고 뺄 수 있는 LIFO (Last In First Out) 형식의 자료 구조입니다.

## 연산

스택은 LIFO를 따르며, 가장 최근에 스택에 추가한 항목이 가장 먼저 제거 됩니다.

- pop(): 스택에서 가장 위에 있는 항목을 제거합니다.
- push(item): item 하나를 스택의 가장 위 부분에 추가합니다.
- peek() : 스택의 가장 위에 있는 항목을 반환합니다.
- isEmpty(): 스택이 비어 있을 때에 true를 반환합니다.

## 구현

```java
public class MyStack {
  private static class StackNode {
    private T data;
    private StackNode next;

    public StackNode(T data) {
      this.data = data;
    }
  }

  private StackNode top;

  public T pop() {
    if (top == null) throw new NoSuchElementException();
    T item = top.data;
    top = top.next;

    return item;
  }

  public void push(T item) {
    StackNode t = new StackNode(item);
    t.next = top;
    top = t;
  }

  public T peek() {
    if (top == null) throw new NoSuchElementException();
    return top.data;
  }

  public boolean isEmpty() {
    return top == null;
  }
}
```

## 배열 vs 스택

문제의 종류에 따라 배열보다 스택에 데이터를 저장하는 것이 적합한 경우가 있습니다.

- 배열과 달리 스택은 상수 시간에 i번째 접근할 수 없습니다.
- 하지만, 스택은 데이터를 추가하거나 삭제하는 연산은 상수 시간에 가능합니다.
- 또한, 배열처럼 원소들을 하나 씩 옆으로 밀어 줄 필요가 없습니다.

즉, 데이터를 탐색하기보다 앞 단의 데이터만 넣고 빼고 하는 과정이 빈번하게 일어나는 경우, 스택 자료 구조가 효율적일 수 있습니다.

## 사용 사례

마지막 데이터를 먼저 처리해야 하는 경우 사용됩니다.

- 함수의 호출
- 재귀 알고리즘
- 후위 표기법 계산
- 웹 브라우저 방문 기록 (뒤로 가기)
- 실행 취소 (Ctrl + Z)
- 역순 문자열 만들기

### 함수의 호출

메모리에는 스택 영역이 존재하는데, 이 곳에는 함수의 호출과 관계 되는 지역 변수와 매개 변수가 저장됩니다. 스택 영역은 함수의 호출과 함께 할당되며, 함수의 호출이 완료되면 소멸합니다.

함수가 호출되면 스택에는 함수의 매개 변수, 호출이 끝난 뒤 돌아갈 반환 주소 값, 함수에서 선언된 지역 변수 등이 저장됩니다. 이렇게 스택 영역에 차례대로 저장되는 함수의 호출 정보를 스택 프레임이라고 합니다. 이러한 스택 프레임 덕분에 함수의 호출이 모두 끝난 뒤에, 해당 함수가 호출되기 이전 상태로 되돌아 갈 수 있습니다.

**동작 방식**

![image](https://user-images.githubusercontent.com/56083021/140273889-48a7cae7-1161-4be5-9ccd-c495371e3cfa.png)

Step 1. 프로그램이 실행되면, 가장 먼저 main() 함수가 호출되어 main() 함수의 스택 프레임이 스택에 저장됩니다.

Step 2. func1() 함수를 호출하면 해당 함수의 매개변수, 반환 주소값, 지역 변수 등의 스택 프레임이 스택에 저장됩니다.

Step 3. func2() 함수를 호출하면 해당 함수의 스택 프레임이 추가로 스택에 저장됩니다.

![Untitled 1](https://user-images.githubusercontent.com/56083021/140274137-00e6efa2-cdbc-4e14-b64c-7ed4a21d0836.png)

Step 4. func2() 함수의 모든 작업이 완료되어 반환되면, func2() 함수의 스택 프레임만이 스택에서 제거됩니다.

Step 5. func1() 함수의 호출이 종료되면, func1() 함수의 스택 프레임이 스택에서 제거됩니다.

Step 6. main() 함수의 모든 작업이 완료되면, main() 함수의 스택 프레임이 스택에서 제거되면서 프로그램이 종료됩니다.

### 재귀 알고리즘

재귀 함수란 자기 자신을 다시 호출하는 함수입니다. 자기 자신을 계속 스택에 넣으면서 종료 조건에 도달하면 스택에서 제거하는 방식과 유사하다는 특징이 있습니다.

**예제 - 깊이 우선 탐색 (DFS)**

깊이 우선 탐색은 루트 노드 (혹은 임의의 노드)에서 시작해서 다음 분기로 넘어 가기 전에 해당 분기를 완벽하게 탐색하는 방법을 말합니다. 이때, 한 번 방문한 노드는 일반적으로 무한 루프의 위험성 때문에 다시 방문하지 않습니다.

![Untitled 2](https://user-images.githubusercontent.com/56083021/140274187-994c6ab3-7098-47a6-91c1-07845e978237.png)

![Untitled 3](https://user-images.githubusercontent.com/56083021/140274225-e6e9eb3c-a510-4d32-bde9-8fad41927cb8.png)

![Untitled 4](https://user-images.githubusercontent.com/56083021/140274228-462307f6-b060-4651-add0-7be537ba31b3.png)

![Untitled 5](https://user-images.githubusercontent.com/56083021/140274229-61cd55cb-25f4-4077-ad14-18762df65fbb.png)

![Untitled 6](https://user-images.githubusercontent.com/56083021/140274233-c84546bc-34aa-4697-b3d5-8031a5f1c790.png)

![Untitled 7](https://user-images.githubusercontent.com/56083021/140274239-5f650c00-739d-4529-aadb-f5cde92d342e.png)

![Untitled 8](https://user-images.githubusercontent.com/56083021/140274240-99a87147-74c4-4260-80d2-802830d8088a.png)

![Untitled 9](https://user-images.githubusercontent.com/56083021/140274245-faf540e7-7cc6-42f1-bb4b-aa6a9fe511d1.png)

![Untitled 10](https://user-images.githubusercontent.com/56083021/140274250-ce42d0de-81d3-4969-8235-a9017f51d4fb.png)

![Untitled 11](https://user-images.githubusercontent.com/56083021/140274252-f321f439-688d-418c-9062-5b1dcbe9d8fb.png)

![Untitled 12](https://user-images.githubusercontent.com/56083021/140274257-fdd0776b-f5af-4b5f-bfb7-0628e42d9965.png)

![Untitled 13](https://user-images.githubusercontent.com/56083021/140274264-069509c1-9a02-4a09-916a-07b1d76cd35c.png)

### 후위 표기법

중위 표기법은 연산자가 피연산자 사이의 위치하는 것으로, 우리가 흔히 사용하는 방식인 `(A + B) * (C + D)` 와 같습니다. 반면, 후위 표기법은 `AB + CD + *` 같이 연산자가 피연산자 뒤에 위치하는 방식을 의미합니다.

**예제1. A * B + C**

![Untitled 14](https://user-images.githubusercontent.com/56083021/140274281-bf81c70f-0201-4c7c-8d45-6cc4530a106c.png)

**예제2. A + B * C**

![Untitled 15](https://user-images.githubusercontent.com/56083021/140274296-0166cbb3-b669-41d1-abe4-5ec367504b01.png)

# Queue

## 개념

큐는 한 쪽 끝에서만 자료를 넣고 뺄 수 있는 FIFO (First In First Out) 형식의 자료 구조입니다.

## 연산

큐는 FIFO를 따르며, 처음에 넣은 항목이 가장 먼저 제거됩니다.

- offer(item): item을 큐의 끝 부분에 추가합니다.
- poll(): 큐의 첫 번째 항목을 제거합니다.
- peek(): 큐에서 가장 위에 있는 항목을 반환합니다.
- isEmpty(): 큐가 비어 있을 때에 true를 반환합니다.

## 구현

큐는 연결 리스트로 구현할 수 있습니다.

```java
public class MyQueue {
  private static class QueueNode {
    private T data;
    private QueueNode next;

    public QueueNode(T data) {
      this.data = data;
    }
  }

  private QueueNode first;
  private QueueNode last;

  public void offer(T item) {
    QueueNode t = new QueueNode(item);

    if (last != null) last.next = t;
    last = t;
    if (first == null) first = last;
  }

  public T poll() {
    if (first == null) return null;
    T data = first.data;
    first = first.next;

    if (first == null) last = null;
    return data;
  }

  public T peek() {
    if (first == null) throw new NoSuchElementException();
    return first.data;
  }

  public boolean isEmpty() {
    return first == null;
  }
}
```

## 사용 사례

데이터가 입력된 시간 순서대로 처리해야 할 필요가 있는 상황에 이용합니다.

- 너비 우선 탐색 (BFS)
- 프로세스 관리
- 페이지 교체 알고리즘
- 인쇄 대기열
- 콜센터 고객 대기 시간
- 프린터의 출력 처리

### 너비 우선 탐색

너비 우선 탐색은 루트 노트 (혹은 임의의 노드)에서 시작해서 인접한 노드를 먼저 탐색하는 방법입니다. 이때, 한 번 방문한 노드는 일반적으로 무한 루프의 위험성 때문에 다시 방문하지 않습니다.

![Untitled 16](https://user-images.githubusercontent.com/56083021/140274320-e2f86ffd-2789-4114-82fa-e9d61f492ee7.png)


### 프로세스 관리

**사전 지식**

프로세스: 메인 메모리에 할당되어 실행 중인 상태의 프로그램을 말합니다.

PCB: 프로세스에 대한 모든 정보가 모여있는 곳입니다.

![Untitled 17](https://user-images.githubusercontent.com/56083021/140274328-61e85937-bdb3-4b1f-9981-60086944b33a.png)

프로세스는 수행하면서 상태가 여러 번 변하는데, 이에 따라 서비스를 받아야 하는 곳이 다릅니다. 그리고 프로세스는 일반적으로 여러 개가 한 번에 수행되므로 그에 따른 순서가 필요합니다. 이러한 순서를 대기하는 공간에 큐가 사용되며, 이들을 프로세스 큐라고 부릅니다.

![Untitled 18](https://user-images.githubusercontent.com/56083021/140274338-d4e7b61c-55eb-4f00-9e87-de893c880b7f.png)

- Job Queue: 하드디스크에 있는 프로그램이 실행되기 위해 메인 메모리의 할당 순서를 기다리는 큐입니다.
- Ready Queue: CPU 점유 순서를 기다리는 큐 입니다.
- Devide Queue: I/O를 하기 위한 여러 장치가 있는데, 각 장치를 기다리는 큐가 각각 존재합니다.

위와 같이 여러 큐가 존재하는데, 각 큐 내부에 저장된 실제 데이터는 각 프로세스의 PCB가 저장되어 있습니다. 그리고 이러한 순서를 기다리는 공간이 있다면 이 순서를 정해주는 알고리즘이 있어야하는데, 그것이 바로 스케줄링입니다.

- Job Queue - Job 스케줄러 (Long-term 스케줄러)
- Ready Queue - CPU 스케줄러 (Short-term 스케줄러)
- Device Queue - Device 스케줄러

Job Queue의 순서를 정해주는 Job 스케줄러를 long-term 스케줄러라고도 하는데, 이는 이 스케줄링이 발생하는 시간이 비교적 오래 걸리기 때문입니다.(대략 초~분) 

반면에 Ready Queue의 스케줄러를 short-term 스케줄러라고도 하는데, 이는 스케줄링이 발생하는 시간이 매우 짧기 때문입니다. CPU 스케줄링은 말 그대로 프로세스가 CPU를 점유하는 순서를 정해주는데 이는 매우 빠른 시간 안에 이루어져야 합니다. 현대 컴퓨터가 여러 프로그램을 동시에 사용하는 것과 같은 효과를 주는 이유가 이 스케줄링 속도가 매우 빠르게 이루어지기 때문입니다.

### 페이지 교체 알고리즘

페이지 교체 알고리즘은 메모리를 관리하는 운영 체제에서 페이지 부재가 발생하여 새로운 페이지를 할당하기 위해 현재 할당된 페이지 중 어느 것과 교체할 지를 결정하는 방법을 의미합니다. OPT, FIFO, LRU, LFU, NUR 등 여러 가지 알고리즘이 존재하지만, 큐와 관련된 FIFO를 설명하겠습니다.

FIFO는 가장 간단한 알고리즘으로 메모리에 올라온 지 가장 오래된 페이지를 교체합니다. 이 알고리즘을 수행하기 위해서 각 페이지가 올라온 시간을 페이지에 기록하거나, 페이지가 올라온 순서를 큐에 저장하는 방식을 사용할 수 있습니다.

![Untitled 19](https://user-images.githubusercontent.com/56083021/140274364-29f1ef36-c935-405c-a778-3d6da0c43dee.png)

FIFO 알고리즘은 이해가 쉽고 구현이 간단하지만, 성능이 언제가 좋지는 않습니다. 위의 그림을 예시로 들면, 페이지 7의 경우 프로세스 초기에 쓰인 후 한동안 쓰이지 않기 때문에 FIFO 교체 방식이 큰 문제를 일으키지는 않습니다. 반면, 페이지 2(9번 째)의 경우 직전 페이지 부재 (page 4)로 인해 페이지 4와 페이지 2가 교체되고 난 후, 또 다시 페이지 2를 사용하기 위해 교체했던 페이지 2를 다시 불러들였습니다. 

![Untitled 20](https://user-images.githubusercontent.com/56083021/140274380-a5529e53-2279-43f5-9268-d18977ec13bc.png)

만약 활발하게 사용 중인 페이지를 계속해서 교체한다면 페이지 부재율이 높아지고 실행 속도가 떨어질 위험이 있습니다.

## 출처

[https://gmlwjd9405.github.io/2018/08/03/data-structure-stack.html](https://gmlwjd9405.github.io/2018/08/03/data-structure-stack.html)

[https://gmlwjd9405.github.io/2018/08/02/data-structure-queue.html](https://gmlwjd9405.github.io/2018/08/02/data-structure-queue.html)

[https://velog.io/@inyong_pang/12강-스택의-응용-수식의-후위-표기법Postfix-Notation](https://velog.io/@inyong_pang/12%EA%B0%95-%EC%8A%A4%ED%83%9D%EC%9D%98-%EC%9D%91%EC%9A%A9-%EC%88%98%EC%8B%9D%EC%9D%98-%ED%9B%84%EC%9C%84-%ED%91%9C%EA%B8%B0%EB%B2%95Postfix-Notation)

[https://velog.io/@codemcd/운영체제OS-5.-프로세스-관리](https://velog.io/@codemcd/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9COS-5.-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EA%B4%80%EB%A6%AC)

[http://tcpschool.com/c/c_memory_stackframe](http://tcpschool.com/c/c_memory_stackframe)

[https://itdexter.tistory.com/86](https://itdexter.tistory.com/86)

## 예상 면접 질문과 답변

Q. 스택은 무엇입니까?

A. 스택은 한 쪽 끝에서만 자료를 넣고 뺄 수 있는 LIFO (Last In First Out) 형식의 자료 구조입니다.

Q. 스택을 언제 사용합니까?

A. 마지막에 있는 데이터가 먼저 나가야 하는 상황일 때 사용합니다. 함수의 호출, 재귀 알고리즘, 후위 표기식, 웹 브라우저에서 뒤로 가기, Ctrl + Z 등에서 활용됩니다.

Q. 큐는 무엇입니까?

A. 큐는 한 쪽 끝에서만 자료를 넣고 뺄 수 있는 FIFO (First In First Out) 형식의 자료 구조입니다.

Q. 큐를 언제 사용합니까?

A. 먼저 들어온 데이터가 먼저 나가야 하는 상황일 때 사용합니다. 너비 우선 탐색, 프로세스 관리, 페이지 교체 알고리즘, 인쇄 대기열, 콜센터 고객 대기 시간, 프린터의 출력 처리 등에서 활용됩니다.
