# 프로세스 제어 블록(PCB, Process Control Block)

## Process Metadata

**프로세스**는 컴퓨터에서 연속적으로 실행되고 있는 동적인 상태의 컴퓨터 프로그램, 즉 **하나의 작업 단위**이다

프로세스는 여러가지 특징을 가지고 있는데, 이러한 특징이 Process Metadata 이다

```
Process Metadata
- process-id : 새로운 프로세스에 시스템이 할당해주는 고유 id  
- process- state : 프로세스의 라이프 타임과 관련된 상태로, waiting, running, ready, blocked, end, suspend-wait, suspend-ready 가 있다  
- priority : 프로세스 스케줄링을 위한 우선순위이다  
- Process Accounting Information : CPU를 사용한 시간, CPU 할당 시간 등이 있다  
- Program Counter : 이전에 배운 PC로 다음 작업할 명령어 위치를 기억한다  
- List of Open Files : 실행 중에 프로그램에 필요한 모든 파일의 정보를 포함한다  
- Process I/0 status information : 해당 프로세스가 실행 중에 할당을 요구한 I/O 장치에 대한 정보를 담는다  
- CPU 레지스터 : context switch가 발생하면 이 때의 레지스터 정보를 기억해서 다시 프로세스가 CPU 할당을 받으면 사용한다. accumulator, index, stack pointer 와 같은 레지스터의 값이 저장된다  
- PCB Pointer : 준비중인 다음 프로세스의 주소를 가리킨다. 준비 상태나, 대기 상태의 큐를 구현할 때 다음을 가리키는 포인터로 사용된다  
- Memory management information : 메모리 관리 정보로 프로세스가 메모리의 어디에 있는 지 나타내는 메모리 정보와 메모리 보호를 위한 경계 레지스터, 한계 레지스터 값등이 저장된다. 또한, segmentation table , page table 등 정보도 보관한다  
- PPID, CPID : 부모 프로세스를 가리키는 PPID, 자식 프로세스를 관리하는 CPID가 저장된다.  
```    

대표적으로 메타데이터에서 관리하는 정보는 아래와 같이 있다

- **process-id** (프로세스 구분자)
메모리에 있는 여러 프로세스들을 구분하기 위해 존재
- **메모리 관련 정보**
프로세스의 메모리 위치 정보 및 메모리 보호를 위한 경계 레지스터와 한계 레지스터 값
CPU는 이를 통해 실행하려는 프로세스가 메모리의 어디에 저장되어 있는지 알 수 있다
- **각종 중간값**
프로세스가 사용했던 중간 값들
여러 프로세스가 번갈아 실행되는 경우, 프로세스는 일정 작업 시간 후 다른 프로세스에 CPU를 넘겨주게 되는데, 이 때 다음 작업을 하게 되었을 때의 위치를 저장한다
    - PC(Program Counter) : 다음 실행할 명령어
    - CPU Register : 연산

이러한 메타데이터를 통해 CPU는 각 프로세스를 구분할 수 있고, 이를 통해 프로세스가 여러개일 때 전부 관리할 수 있다

즉, **CPU가 프로세스를 처리하는데 필요한 다양한 정보를 가지고 있다**

```
💡 Process = Program + PCB
```

이렇게 프로세스 여러개를 CPU 스케쥴링을 통해 관리하는 것을 Process Management 라고 부른다

## Metadata의 **생성과 PCB**

이 Metadata는 프로세스가 생성되면 PCB(Process Control Block)에 저장된다

1. 프로그램 실행
2. 프로세스 생성 (프로그램을 메모리에 적재)
3. 프로세스 메타데이터 생성
4. 메타데이터를 PCB에 저장

이때, 한 PCB에는 한 프로세스의 정보가 담긴다

## PCB의 필요성과 관리

프로세스가 여러개 생성될 때, 즉 여러개의 프로그램이 실행되고 있을때, CPU는 프로세스의 상태에 따라 **교체 작업을 수행**한다

```
💡 교체 작업 : Interrupt 발생 → 할당 받은 프로세스 Waiting 상태로 변경 → 다른 프로세스를 Running 상태로 변경
```

따라서, **수행 대기 중인 프로세스에 관한 저장 값을 PCB에 저장**해두어야 한다

PCB는 Linked List 방식으로 관리되어 삽입/삭제가 용이하다

따라서 프로세스가 생성될 때 PCB List Head에 PCB가 삽입되고, 프로세스가 완료되면 PCB가 삭제되는 식으로 관리하는 것이다

# 문맥 교환(Context Switching)

수행 중인 Task(Process/Thread)가 변경될 때, CPU의 Register 정보가 변경되는 것을 뜻한다

즉, **이전 프로세스의 상태를 PCB에 보관**하고, **다른 프로세스의 정보를 PCB에서 읽어와 CPU Register에 적재**하는 과정이다

```
💡 Context : CPU가 다루는 프로세스/스레드에 대한 정보로, 대부분 register에 저장되어 있으며 PCB로 관리한다
```

## 프로세스의 상태

프로세스는 각각 상태를 가지고 있으며, 크게 활성 상태(active status)와 보류 상태(suspend status)가 있다

### 활성 상태

![https://media.vlpt.us/images/chappi/post/3c1e88b9-ffc5-403b-9a59-af3d1f9ae9a4/2.png](https://media.vlpt.us/images/chappi/post/3c1e88b9-ffc5-403b-9a59-af3d1f9ae9a4/2.png)

위 그림의 5가지 상태는 활성 상태이다

- **New**
프로세스가 메모리에 올라와 **실행 준비를 완료**한 상태이며, **PCB가 생성**된다
- **Ready**
생성된 프로세스가 CPU를 받을 때까지 기다리는 상태
- **Running**
Ready 상태에 있던 프로세스가 CPU를 받아 실행되는 상태
- **Waiting(block)**
Running 상태에 있던 프로세스가 입출력을 요청한 후 입출력이 완료될 때까지 기다리는 상태
- **End(terminate)
프로세스가 종료**되는 상태이며, 사용했던 **데이터를 삭제**하고 **PCB를 폐기**한다

활성 상태에 있는 **프로세스는 메모리에는 올라와 있으며 실행이 되고 있거나 잠시 멈춘 상태**이다

### 보류 상태

활성 상태와 달리 보류 상태에 있는 프로세스는 **메모리에 적재되어 있지 않으며**, 일시 정지 상태라고도 부른다

메모리에서 쫓겨난 프로세스는 Swap 영역에 보관된다

## 상태 변경

CPU를 차지하여 Running 상태이던 프로세스가 나가고 새로운 프로세스가 Running 상태가 되는 작업이며, 이 때 **각 프로세스의 PCB가 변경**된다

즉, 실행 상태에서 나가는 프로세스의 PCB는 작업 내용을 저장하며, 실행 상태로 들어오는 프로세스는 PCB 내용을 바탕으로 CPU가 세팅된다

프로세스의 변경은 다양한 상황에서 발생한다

- Running → Ready
실행 중이던 프로세스가 작업 시간을 다 사용하여 timeout이 발생하면 준비 상태로 옮긴다
- Running → End
실행 중이던 프로세스가 작업 시간 안에 작업을 끝내면 종료 상태로 옮긴다
- Running → Waiting
실행 중이던 프로세스가 입출력을 요청하면 대기 상태로 옮긴다
- Waiting → Interrupt → Ready
입출력을 요청하여 대기 상태에 있던 프로세스의 요청이 완료되면 Interrupt가 발생하여 해당 프로세스를 깨워서 준비 상태로 옮긴다

즉, **Context Switching은 하드웨어를 통한 I/O 요청이나 CPU 스케쥴링에 의한 Interrupt에 의해 발생한다**

## Cost

Context Switching 발생 시 많은 Cost가 소요된다

- Cache 초기화
- Memory Mapping 초기화
- Kernel은 항상 실행되어야 함

따라서 잦은 Context switching은 성능 저하를 야기한다

# 참고

## 결론

### PCB의 역할은 무엇인가

PCB에는 프로세스ID나 각종 메모리나 중간값과 같은 프로세스 메타데이터를 저장하는 공간입니다
PCB에 저장된 정보를 통해 CPU는 각 프로세스를 구분하고 관리할 수 있으며, context switching이 발생하였을 때 이를 이용합니다

### Context Switching 이란?

현재 수행 중인 프로세스의 상태 정보를 PCB에 저장하고 다음 수행할 프로세스의 PCB에서 정보를 읽어와 CPU 레지스터를 세팅하는 과정입니다

### Context Switching은 언제 발생하는가

실행되고 있는 프로세스가 빠지고 새로운 프로세스가 CPU를 받을 때 발생합니다. CPU 스케쥴링에 의해 할당된 작업 시간이 끝나 timeout이 발생했거나, 프로세스의 작업이 끝났거나, 실행중이던 프로세스가 입출력 요청을 하는 등의 이유로 interrupt가 발생했을 때 context switching이 이루어집니다.

### Context Switching 의 필요성 무엇인가

CPU는 한번 하나의 프로세스만 수행할 수 있지만, 실생활에서 우리는 여러개의 프로세스를 동시에 수행하고자 합니다
따라서 CPU는 여러개의 프로세스를 번갈아가며 수행해서 동시에 수행하는 것처럼 보이게 하기 위해 Context Switching을 사용합니다

## 출처

- [https://velog.io/@chappi/OS는-할껀데-핵심만-합니다.-2편-프로세스와-프로그램](https://velog.io/@chappi/OS%EB%8A%94-%ED%95%A0%EA%BB%80%EB%8D%B0-%ED%95%B5%EC%8B%AC%EB%A7%8C-%ED%95%A9%EB%8B%88%EB%8B%A4.-2%ED%8E%B8-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4%EC%99%80-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%A8)
- [https://github.com/gyoogle/tech-interview-for-developer/blob/master/Computer Science/Operating System/PCB %26 Context Switcing.md](https://github.com/gyoogle/tech-interview-for-developer/blob/master/Computer%20Science/Operating%20System/PCB%20%26%20Context%20Switcing.md)
