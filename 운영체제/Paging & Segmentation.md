# 메모리

## 메인 메모리(Main Memory, Physical Memory, 주기억장치)

**CPU가 직접 접근할 수 있는 기억장치**로, 프로세스가 실행되려면 프로그램이 메모리에 올라와 있어야 한다

프로그램이 실행되려면 프로그램 코드를 메인 메모리에 적재하고, CPU가 이에 접근하여 실행되는 것인데, 프로그램 용량이 메인 메모리보다 크다면?

## 가상 메모리(Virtual Memory)

실제 **물리 메모리 개념**과 **사용자의 논리 메모리 개념**을 분리한 것
⇒ 가상 주소 공간(한 프로세스가 **메모리에 저장되는 논리적인 모습**을 가상 메모리에 구현한 공간)

메모리의 공간은 한정적이므로, 사용자에게 더 많은 메모리를 제공하기 위해 **가상 주소**를 사용한다
**메모리 관리 장치**(MMU, Memory Management Unit)는 가상 주소를 이용해 실제 데이터가 담겨 있는 주소로 변환해준다

### 왜 필요한가

**물리 메모리의 한계**

- 프로그램 코드 전부를 물리 메모리에 올려야한다
- 그래서 메모리 용량보다 큰 프로그램은 실행시킬 수 없다
- 여러 프로그램을 메모리에 다 올리기에는 용량의 한계가 있다
- 프로그램을 교체하면서 올리자니 메모리 교체 성능 문제가 발생한다

**가상 메모리의 장점**

- 프로그램 용량이 실제 물리 메모리보다 커도 된다
- 전체 프로그램이 물리 메모리에 올라와있지 않아도 된다
- 즉, **물리 메모리 크기에 제약을 받지 않는다**
- 따라서 더 많은 프로그램을 동시에 실행 할 수 있다
    - 응답시간은 유지
    - CPU 이용률과 처리율은 증가

⇒ 다중 프로그래밍을 실현하기 위해 물리 메모리의 제약을 보완하고 프로세스 전체를 메모리에 올리지 않고도 실행할 수 있도록 해준다

## 주기억장치 & 보조기억장치

### 가상 메모리의 구현

운영 체제는 물리 메모리의 제약을 갖고 있는 주 기억 장치를 보조하기 위해 디스크를 보조 기억 장치(Paging Space)로 사용한다

![https://mblogthumb-phinf.pstatic.net/MjAxODA5MzBfMjUx/MDAxNTM4Mjc3NTI1Njg0.yTeRYit5DAiXpK0G7O3kkrLG284PjiJa2VZU3TCwFqIg.cxlmtf3mYPzwII9hwyG41Zkhnz1sdfL1FJyK6AWYynsg.PNG.hbom20/HardwareOrganiztionOfComputerSystem.png?type=w800](https://mblogthumb-phinf.pstatic.net/MjAxODA5MzBfMjUx/MDAxNTM4Mjc3NTI1Njg0.yTeRYit5DAiXpK0G7O3kkrLG284PjiJa2VZU3TCwFqIg.cxlmtf3mYPzwII9hwyG41Zkhnz1sdfL1FJyK6AWYynsg.PNG.hbom20/HardwareOrganiztionOfComputerSystem.png?type=w800)

즉, 메인 메모리(주 기억 장치)와 디스크의 페이징 스페이스(보조 기억 장치)를 묶어 하나의 메모리처럼 동작하게 하며, 이를 통해 메인 메모리의 한계를 넘는 메모리 사용을 가능하게 하는 가상 메모리를 구현한다

### Swapping

CPU 할당 시간이 끝난 프로세스의 메모리를 보조 기억장치로 내보내고(swap-out) 다른 프로세스의 메모를 불러오는(swap-in) 작업을 **Swap**이라고 한다
이러한 swap 작업에는 디스크 전송 시간이 들기 때문에 메모리 공간이 부족할 때 **Swapping**이 이루어진다

*이때 CPU가 직접 접근할 수 있는 메모리를 활성 가상 메모리(Active Virtual Memory), 페이징 스페이스를 비활성 가상 메모리(Inactive Virtual Memory)라고 부른다*

# 메모리 관리

다중 프로그래밍 시스템에 여러 프로세스를 수용하기 위해 **주기억장치**(RAM)**를 동적 분할**하는 **메모리 관리 작업**이 필요하다

즉, 하드 디스크에 있는 프로그램을 어떻게 메인 메모리에 적재할 것인가 생가해야한다

### 연속 메모리 관리

프로그램 전체가 하나의 커다란 공간에 연속적으로 할당되어야 한다

- **고정 분할 기법** : 주기억장치가 고정된 파티션으로 분할
→ 내부 단편화 발생
- **동적 분할 기법** : 파티션들이 동적 생성되며 자신의 크기와 같은 파티션에 적재
→ 외부 단편화 발생

이렇게 연속 메모리 관리 기법을 사용할 경우, 단편화 현상이 발생하는 것이 문제이다

단편화 현상이란 무엇일까

### 단편화(Fragmentation)

기억 장치의 빈 공간 또는 자료가 여러 조각으로 나뉘는 현상
프로세스들이 메모리에 적재되고 제거되는 일이 반복되다보면, 프로세스들이 차지하는 메모리 틈 사이에 사용하지 못할 만큼의 작은 자유 공간들이 늘어나게 된다

이러한 단편화는 2가지로 나뉜다

**내부 단편화**

- 프로세스가 사용하는 메모리 공간에 남는 부분
- 프로세스가 요청한 양보다 더 많은 메모리를 할당할 때 발생하며, 메모리 분할 자유 공간과 프로세스가 사용하는 공간의 크기 차이이다
    
    ![내부 단편화](https://trello-attachments.s3.amazonaws.com/5e8aea3aaf239f64e5fe7306/820x393/d6b06e1de4afa891f98f931148041065/image.png)
    
    내부 단편화
    

**외부 단편화**

- 메모리 공간 중 사용하지 못하게 되는 부분
- 메모리가 할당 및 해제 작업의 반복으로 작은 메모리가 중간중간에 존재 중간중간에 생긴 사용하지 않는 메모리가 존재해서 총 메모리 공간은 충분하지만 실제로 할당할 수 없는 상황
- 외부 단편화를 해결하기 위해 **압축**을 이용하여 프로세스가 사용하는 공간을 한쪽으로 몰 수 있지만, 작업 효율이 좋지는 않다
    
    ![외부 단편화](https://trello-attachments.s3.amazonaws.com/5e8aea3aaf239f64e5fe7306/820x347/3113cb036860ef7b97257f0ca5cc9451/image.png)
    
    외부 단편화
    

단편화가 많이 발생하는 메모리 관리 기법은 좋은 방법이라고 할 수 없을 것이다

### 불연속 메모리 관리

프로그램의 일부가 서로 다른 주소 공간에 할당될 수 있는 기법

앞서 봤던 단편화 문제를 해결하기 위해 제시된 기법으로, 외부 단편화 해소를 위한 페이징과 내부 단편화 해소를 위한 세그멘테이션, 2가지로 나뉜다

# 페이징(Paging)

프로세스를 일정한 크기의 페이지로 분할해서 메모리에 적재하는 방식

- **페이지** : 고정 사이즈의 가상 메모리 내 프로세스 조각
- **프레임** : 페이지 크기와 같은 주기억장치 메모리 조각

→ 하나의 프로세스가 사용하는 메모리 공간이 연속적이어야 한다는 제약을 없애는 메모리 관리 방법

## 페이징 테이블(Paging Table)

![페이징 테이블의 매핑](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbcWBuK%2FbtquS9IGzkq%2FbetCj8R8f7gULsxnkEaiMK%2Fimg.png)

페이징 테이블의 매핑

앞서 봤듯이, 물리 메모리는 고정 크기의 프레임으로, 가상 메모리는 고정 크기의 페이지로 분리되어있다. 개별 페이지는 순서에 상관 없이 물리 메모리에 있는 프레임에 mapping 되어 저장된다.

즉, 모든 프로세스는 하나의 **페이징 테이블**을 가지고 있으며, 여기에는 메인 메모리에 적재되어 있는 **페이지 번호**와 해당 페이지가 위치한 메인 메모리의 **시작 주소**가 있다
이를 통해 하나의 프로세스를 나눈 가상 메모리 페이지들이 각각 실제 메인 메모리의 어디 프레임에 적재되어 있는지 알아낼 수 있다

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FVLbF6%2FbtquTSGsIis%2FyHRXYkjCLqbC2YnxFug5W1%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FVLbF6%2FbtquTSGsIis%2FyHRXYkjCLqbC2YnxFug5W1%2Fimg.png)

위 PMT(Page Mapping Table, 페이징 테이블)에서는 P1 프로세스의 0번 째 페이지가 메인 메모리의 5번째 프레임에 있는 것을 알 수 있다

## 논리주소와 페이지 테이블

앞서 **메모리 관리 장치**(MMU, Memory Management Unit)는 가상 주소(논리 주소)를 이용해 실제 데이터가 담겨 있는 주소로 변환해준다고 했었다

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbZc9zQ%2FbtquUA6o3Yy%2FHKkfROoG1131U2MVxJOmF1%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbZc9zQ%2FbtquUA6o3Yy%2FHKkfROoG1131U2MVxJOmF1%2Fimg.png)

**논리 주소**(Logical Address)는 <page, offset>과 같은 형태로 구성되는데, 이를 이용해 **물리 주소**(Physical Address)로 변환해주는 것이다

![가상 주소 → 물리 주소 변환 전체 과정](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FAZBpv%2FbtquWNjFzyJ%2FPpNneWZAPAx2klg0sVp0B0%2Fimg.png)

가상 주소 → 물리 주소 변환 전체 과정

## 페이징의 장단점

**장점**

- 논리 메모리는 물리 메모리에 저장될 때 연속되어 저장될 필요가 없고, 물리 메모리의 남는 프레임에 적절히 배치되기 때문에 **외부 단편화가 생기지 않는다**

**단점**

- **내부 단편화 문제**
    
    ![https://t1.daumcdn.net/cfile/tistory/263A6A435909867A0F](https://t1.daumcdn.net/cfile/tistory/263A6A435909867A0F)
    
    → 페이지 단위를 작게 하면 해결할 수 있지만, page mapping 과정이 복잡해져 오히려 비효율적이다
    

# 세그멘테이션(Segmentation)

프로세스를 물리적 단위인 페이지가 아닌 논리적 단위인 세그먼트로 분할해서 메모리에 적재하는 방식

*돼지를 도축할 때, 페이징은 돼지를 같은 크기로 잘라서 보관하는 것이라면, 세그멘테이션은 부위별로 잘라서 보관하는 것이다*

- **세그먼트** : 가상 메모리를 서로 크기가 다른 논리적 단위로 분할한 것

각 세그먼트는 연속적 공간에 저장되며, 크기가 가변적이기 때문에 미리 메모리를 분할할 수는 없고 메모리에 적재될 때 빈 공간을 찾는다

## 논리적 단위란?

프로세스가 동작하려면 기본적으로 코드, 데이터, 스택, 3가지 세그먼트를 가지고 있어야 한다
코드에는 main 함수를 포함하여 다른 함수나 루틴이 있을 수 있고, 데이터에는 구조체나 배열이 있을 수 있다

이렇게 세그먼트는 의미가 같은 논리적 내용을 기준으로 프로그램을 분할하기 때문에 크기가 같지 않은 것이다

## 세그먼트 테이블

분할 방식을 제외하곤 페이징과 세그멘테이션이 동일하기 때문에, 매핑 테이블의 동작 방식도 동일하다

다만 논리 주소의 앞 비트들은 페이징 번호가 아니라 세그먼트 번호가 될 것이다
즉, <segment, offset> 형태로 구성되며, 세그먼트 번호를 통해 세그먼트의 기준(세그먼트의 시작 물리 주소)과 한계(세그먼트의 길이)를 파악할 수 있다

## 세그멘테이션의 장단점

**장점**

- **내부 단편화 문제가 해소된다**
- **보호와 공유 기능**을 수행할 수 있다
프로그램의 중요한 부분과 안중요한 부분을 분리하여 저장할 수 있고, 같은 코드 영역은 한번에 저장할 수 있다.

**단점**

- **외부 단편화 문제가 커진다**

# 페이징(Paging)과 세그멘테이션(Segmentation)

### 단순 페이지

- 각 프로세스는 프레임들과 같은 길이를 가진 균등 페이지로 나뉨
- 외부 단편화가 생기지 않음
- 소량의 내부 단편화 존재

### 단순 세그멘테이션

- 각 프로세스는 여러 세그멘트들로 나뉨
- 내부 단편화가 생기지 않음
- 메모리 효율 개선
- 동적 분할을 통한 오버헤드 감소
- 외부 단편화 존재

### 가상 메모리 페이징

- 단순 페이징과 비교해 프로세스 페이지 전부를 로드 시킬 필요가 없음
- 필요한 페이지가 있으면 나중에 자동으로 불러들임
- 외부 단편화가 생기지 않음
- 복잡한 메모리 관리로 오버헤드가 발생

### 가상 메모리 세그멘테이션

- 필요하지 않은 세그먼트들은 로드되지 않음
- 필요한 세그먼트가 있을 때 나중에 자동으로 불러들임
- 내부 단편화가 생기지 않음
- 복잡한 메모리 관리로 오버헤드 발생

# 비고

## 결론

### 페이징 또는 세그멘테이션을 사용하는 이유는 무엇일까

프로그램을 실행하기 위해 코드를 디스크에서 메인 메모리로 적재하는 과정에서 단편화가 발생할 수 밖에 없습니다. 이렇게 단편화가 많이 발생하면 사용하지 못하는 메모리 공간이 많아져 낭비하게 되기 때문에 최대한 피해야 하는 문제입니다. 최초 적합과 최적 적합, 압축 등의 방식을 통해서도 해결할 수 있지만, 메모리 계산의 비용이 적은 페이징 또는 세그멘테이션을 사용합니다

### 페이징의 특징은 무엇인가

페이징은 프로그램을 실행하기 위해 코드를 메모리에 적재하기 위해 사용하는 기법으로, 불연속 메모리 관리 기법이라는 특징이 있습니다. 다시 말해 프로그램 전체가 전체가 메모리에 연속적으로 올라가 있는 것이 아니라 페이지라는 고정된 크기로 분할되어 올라가 있습니다.

페이징의 또 다른 특징은 연속 메모리 관리 기법의 단점 중 하나였던 외부 단편화를 해결할 수 있다는 점입니다. 

### 페이징과 세그멘테이션의 차이는 무엇인가

페이징과 세그멘테이션 모두 프로그램을 실행하기 위해 디스크에 있는 내용을 분할하여 메모리에 적재하는 불연속 메모리 관리 기법입니다. 둘의 차이는 프로그램을 어떻게 분할할 것인가입니다. 페이징의 경우 프로그램을 같은 크기의 페이지로 분할하는 데에 비해, 세그멘테이션은 논리적 의미를 기준으로 세그먼트를 분할합니다.

## 출처

- [tech-interview-for-developer](https://github.com/gyoogle/tech-interview-for-developer/blob/master/Computer%20Science/Operating%20System/Paging%20and%20Segmentation.md)
- [Paging and Segmentation.pdf](https://github.com/gyoogle/tech-interview-for-developer/blob/master/Computer%20Science/Operating%20System/Paging%20and%20Segmentation.pdf)
- [Interview_Question_for_Beginner](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/OS#%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B4%80%EB%A6%AC-%EC%A0%84%EB%9E%B5)
- [운영체제 측면에서 Memory(메모리), Virtual Memory(가상메모리)](https://brocess.tistory.com/268)
- [운영체제 21장 - 메모리 관리(8) : 가상 메모리 -](https://copycode.tistory.com/113)
- [메모리 단편화와 내부 단편화, 외부 단편화 개념 및 차이점?](https://junghyun100.github.io/%EB%A9%94%EB%AA%A8%EB%A6%AC%EB%8B%A8%ED%8E%B8%ED%99%94/)
- [[OS, 운영체제] 메모리 관리, 가상메모리](https://highsg.tistory.com/28)
- [운영체제 18장 - 메모리 관리(5) : 페이징](https://copycode.tistory.com/102?category=740133)
