# CPU Scheduling

## CPU I/O Burst Cycle

[https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fcdg2Xb%2Fbtq3r03Arsh%2Fk7Fjr3uPPKlAL5A33MZV4k%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fcdg2Xb%2Fbtq3r03Arsh%2Fk7Fjr3uPPKlAL5A33MZV4k%2Fimg.png)

프로세스의 실행은 CPU 실행(execution)과 I/O 요청 대기(I/O wait)가 번갈아가며 이루어지는데, 이를 CPU I/O Burst Cycle이라고 한다

CPU 스케줄링을 통해 이 CPU Burst Distribution을 관리한다

![https://raw.githubusercontent.com/zoomKoding/zoomKoding.github.io/source/assets/_posts/os5-1.png](https://raw.githubusercontent.com/zoomKoding/zoomKoding.github.io/source/assets/_posts/os5-1.png)

## CPU 스케줄링이란?

작업을 처리하기 위해서 프로세스들에게 CPU를 할당하기 위한 정책을 계획하는 것

![https://media.vlpt.us/images/torch-ray/post/07c61819-369d-4fa6-95ce-432d810b0354/1_B8DCskub0VXdf3liNq6Uxg.jpeg](https://media.vlpt.us/images/torch-ray/post/07c61819-369d-4fa6-95ce-432d810b0354/1_B8DCskub0VXdf3liNq6Uxg.jpeg)

CPU 스케줄링 대상 프로세스는 Ready Queue에 있는 프로세스들로, 스케줄링 정책에 따라 Queue에 정렬을 한 후, 앞에 있는 프로세스부터 CPU를 주게 된다

## Dispatcher

Scheduler에는 multiprogramming에 이용되는 long-term scheduler와 CPU scheduling에 이용되는 short-term scheduler가 있다

<aside>
💡 넓은 의미의 스케줄링 = 스케줄링(Ready Queue 정렬) + Dispatch(프로세스를 CPU에 올림)

</aside>

## CPU 스케줄링의 필요성

CPU를 효율적으로 사용하기 위해 프로세스들을 잘 배정해야 한다

Ready Queue에 있는 프로세스 대상으로 CPU를 할당하는 순서와 방식을 결정하는 것을 의미한다

## Scheduling Criteria : Goals

수 많은 프로세스들을 어떤 순서로 정렬할지 정책을 수립하는 것이 스케줄링이라면, "좋은" 정렬 방법과 "나쁜" 정렬 방법이 있을 것이다
이렇게 스케줄링 알고리즘을 평가할 수 있는 기준을 **Scheduling Criteria**라고 한다

1. **CPU Utilization** (Keep the CPU as busy as possible)
CPU가 쉬고 있지 않게 해야 한다
2. **Throughput** (Number of processes that complete their execution per time unit)
단위 시간 당 처리량
→ # of instructions / second
3. **Turnaround Time** (Amount of time to execute a particular process)
수행을 요청 한 후 작업이 끝날 때까지 걸린 시간
4. **Waiting Time** (Amount of time a process has been waiting in the ready queue)
수행을 요청 한 후 작업이 시작될 때까지 걸린 시간
→ Running이 된 시간 - Ready queue에 들어간 시간
5. **Response Time** (Amount of time it takes from when a request was submitted until the first response is produced)
요청 이후 응답 시간

CPU Utilization, Throughput은 늘리고, Time 지표들은 감소시키는 것이 스케줄링 목표가 된다

## 시스템 별 목표

CPU Scheduling의 세부적인 목표는 시스템 마다 다르다

**Batch System**

배치 시스템, 한번에 하나의 프로그램만 수행하는 것

- 가능한 한 많은 일을 수행하기 위해 throughout과 CPU utilization 이 중요

**Interactive System**

사용자가 컴퓨터 앞에서 대화형으로 동작하는 시스템

- Response Time → 프로세스가 Ready queue에서 대기하는 시간을 최소화한다
- Waiting Time → 프로세스가 Wait queue에서 대기하는 시간을 최소화한다
- Proportionality → 사용자가 요구하는 바를 이루어야 한다

이러한 시스템의 경우, Time Sharing 기법을 이용해야 한다

**Real-time System**

시간 제약 조건이 걸려있는 시스템

- Meeting Deadlines
- Predictability

# CPU Scheduling Methods

## 분류

### 방법에 따라

### 1. 비선점 (Nonpreemptive)

프로세스 종료 또는 입출력 등의 이벤트가 있을 때까지 실행을 보장

**장점**

- 모든 프로세스들에게 공정
- 응답 시간 예측 가능

**단점**

- 짧은 작업을 수행하는 프로세스라도 긴 작업이 종료될 때 까지 기다려야하는 경우 발생

### 2. 선점 (Preemptive)

OS가 CPU의 사용권을 선점할 수 있는 경우에 강제 회수

**장점**

- 높은 우선순위를 가진 프로세스를 빠르게 처리하려는 시스템에 유용
- 빠른 응답 시간을 요구하는 시분할 시스템에 유용

**단점**

- 높은 우선순위를 가진 프로세스들이 들어오는 경우 Overhead 발생

### 알고리즘에 따라

### FCFS (First Come First Served)

큐에 도착한 순서대로 CPU 할당

- CPU burst가 완료될 때 까지 CPU를 반환하지 않는다
- 할당되었던 CPU가 반환될 때에만 스케쥴링이 이루어진다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dea15609-4237-4806-be26-90da3439e3ac/Untitled.png)

**장점**

- 개발 용이
- **공평성 유지 (Fair)**
- No indefinite postponement (No Starvation)

**한계**

- **Convoy Effect**
    - 소요 시간이 긴 프로세스가 먼저 도달할 경우 효율성이 낮아진다
    - 실행 시간이 짧은 작업이어도 뒤로 가면 대기 시간이 길어진다
    
    → SJF 알고리즘 등장
    

### SJF (Shortest Job First)

[https://jhnyang.tistory.com/155?category=815411](https://jhnyang.tistory.com/155?category=815411)

수행 시간이 가장 짧다고 판단되는 작업 우선 수행

- 평균대기 시간이 짧으며, 짧은 작업에 유리하다

**한계**

- **Starvation**
    - 사용 시간이 긴 프로세스는 영원히 CPU를 할당받지 못할 수도 있다

### HRN (Highest Response-ratio Next)

우선순위를 계산하여 점유 불평등을 보완 (SJF 단점 보완)

<aside>
💡 우선 순위 = (대기시간 + 실행시간) / (실행시간)

</aside>

### SRTF (Shortest Remaining Time First)

현재 수행 중인 프로세스의 남은 burst time보다 더 짧은 CPU burst time을 가진 새로운 프로세스가 도착할 경우 CPU 강제 회수

- 새로운 프로세스가 도착할 때마다 스케줄링이 이루어진다

**한계**

- Starvation
- 새로운 프로세스가 도달할 때마다 스케줄링을 다시 하기 때문에 CPU 사용 시간을 측정할 수가 없다

### Priority Scheduling

[https://jhnyang.tistory.com/132?category=815411](https://jhnyang.tistory.com/132?category=815411)

정적/동적으로 우선순위를 부여하여 우선순위가 높은 순서대로 처리

<aside>
💡 선점형/비선점형 모두에 접목시킬 수 있다

</aside>

**한계**

- Starvation
- Indefinite Blocking(무기한 봉쇄)
실행 준비는 되어 있으나 CPU를 사용하지 못하는 프로세스가 CPU를 무한정 기다리는 상태

→ Aging 기법으로 문제를 해결할 수 있다

### Round Robin

[https://jhnyang.tistory.com/158?category=815411](https://jhnyang.tistory.com/158?category=815411)

FSFC에 의해 프로세스를 받아 각 프로세스에 동일한 시간의 Time Quantum만큼 CPU를 할당

- 현대적인 CPU 스케줄링 방식
- 할당 시간이 끝나면 프로세스는 선정 당하고 Ready Queue의 제일 뒤에 삽입된다
- CPU 사용시간이 랜덤한 프로세스들이 섞여 있을 경우에 효율적이다
- 프로세스의 Context를 save할 수 있기 때문에 가능하다

<aside>
💡 Time Quantum (Time Slice) : 실행의 최소 단위 시간

</aside>

**장점**

- Response time이 빨라진다
N개의 프로세스가 Ready queue에 있고 할당시간이 q(Time Quantum)인 경우 각 프로세스는 q 단위로 CPU 시간의 1/n을 얻는다
→ 어떤 프로세스도 (n-1) * q * Time unit 이상 기다리지 않는다
- 프로세스가 기다리는 시간이 CPU를 사용할 만큼 증가하는 공정한 스케줄링이다

**한계(주의점)**

- Time Quantum이 크면 FCFS와 같아진다
- Time Quantum이 작으면 Context Switching이 잦아져서 Overhead가 증가한다

→ 적당한 Time Quantum을 설정하는 것이 중요하다

### Multilevel-Queue (다단계 큐)

[https://jhnyang.tistory.com/28?category=815411](https://jhnyang.tistory.com/28?category=815411)

작업들을 여러 종류의 그룹으로 나누어 여러 개의 Queue를 사용

우선순위가 낮은 큐들이 실행 못하는 것을 방지하고자 큐마다 다른 Time Quantum을 설정해준다
우선 순위가 높은 큐에는 작은 Time Quantum을, 낮은 큐에는 큰 Time Quantum을 할당하는 식이다

### Multilevel-Feedback-Queue (다단계 피드백 큐)

[https://jhnyang.tistory.com/156?category=815411](https://jhnyang.tistory.com/156?category=815411)

- 다단계 큐에서 자신에게 할당된 Time Quantum을 다 사용한 프로세스는 밑으로 내려가고 Time Quantum을 다 채우지 못한 프로세스는 원래 큐 그대로 (Time Quantum을 다 채운 프로세스는 CPU burst 프로세스로 판단하기 때문)
- 짧은 작업에 유리, 입출력 위주의 작업에 우선권을 준다
- 처리 시간이 짧은 프로세스를 먼저 처리하기 때문에 Turnaround 평균 시간을 줄여준다

# 비고

## 결론

### 각 스케줄링 방식에 대한 설명

각 방식의 작동 방법, 특징 및 장단점을 말할 수 있어야합니다

- FCFS
- SJF
- HRN
- SRTF
- Priority
- Round Robin
- Multilevel-Queue
- Multilevel-Feedback-Queue

## 출처

- [https://github.com/gyoogle/tech-interview-for-developer/blob/master/Computer Science/Operating System/CPU Scheduling.md](https://github.com/gyoogle/tech-interview-for-developer/blob/master/Computer%20Science/Operating%20System/CPU%20Scheduling.md)
- [https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/OS#cpu-스케줄러](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/OS#cpu-%EC%8A%A4%EC%BC%80%EC%A4%84%EB%9F%AC)
- [https://www.notesjam.com/2018/11/scheduling-algorithms-examples.html](https://www.notesjam.com/2018/11/scheduling-algorithms-examples.html)
- [https://jhnyang.tistory.com/category/별걸다하는 IT/운영체제 OS](https://jhnyang.tistory.com/category/%EB%B3%84%EA%B1%B8%EB%8B%A4%ED%95%98%EB%8A%94%20IT/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C%20OS)
