---
title : Process Scheduling
categories : [Operating System]
date : 2020-04-07
---

&nbsp;

## 프로세스 스케쥴링

&nbsp;

멀티프로그래밍의 목적은 CPU의 이용을 극대화 하기 위해 

CPU가 멈추지 않고 어떤 프로세스이건 실행하도록 하는데에 있다.

time-sharing 은 프로그램의 실행 중간에 사용자와 상호작용 할 수 있도록

프로세스 간에 CPU를 빈번하게 교체하는 것이다.

&nbsp;

**프로세스 스케쥴러**는 time sharing 을 위해 프로세스의 CPU를 빈번하게 바꾼다.

이를 위해 프로세스들의 **스케쥴링 큐**가 필요하다. 

이 스케쥴링 큐는 스케쥴러의 선택 대상이 되는 프로세스를 모아놓은 큐이다.

스케쥴링 큐에는 많은 큐가 있고, 스케쥴링 큐마다 해당 큐에 대해 동작하는 스케쥴러가 존재한다.

&nbsp;

<img src="https://user-images.githubusercontent.com/22045424/78581202-87e6b180-786e-11ea-9228-e253d72fa351.png" width=50%>

&nbsp;

위 그림은 커널 내의 스케쥴링 큐이다.

스케쥴링 큐마다 head가 있고

해당 큐에 속해야 하는 프로세스들의 PCB가 연결리스트로 매달려 있다.



스케쥴러의 종류는 다음과 같다.

* Ready Queue 

  메인 메모리에 있는 프로세스의 집합이다. 

  ready 상태이며 즉시 실행이 가능한 프로세스들이 여기에 존재한다.

  위 그림에서 첫번째 큐가 Ready queue 이다.

  

* Device Queue

  입출력 장치를 위해 기다리는 프로세스들이다. 

  여기 있는 프로세스들은 waiting 상태이다. 

  이 큐는 장치마다 하나씩 존재하며 해당 장치에 대해 

  입출력을 개시하고 완료를 기다리는 프로세스들이 속한다.

  위 그림에서 첫번째의 ready queue를 제외한 것들이 이에 속한다.

  현재 네 번째 줄의 disk unit0의 device queue에는

  3번, 14번, 6번 프로세스들이 여기에 입출력을 요청하고 완료를 기다리고 있는 것이다.

  

* Job Queue

  시스템 내의 전체 프로세스의  목록이다.

&nbsp;

프로세스는 자신의 생명주기동안 이런 저런 스케쥴링 큐들로 옮겨다닌다.

&nbsp;

&nbsp;

## 스케쥴러

&nbsp;

스케쥴러는 스케쥴링 큐에서 특정 프로세스를 선택하는 동작을 하는 커널의 모듈이다.

스케쥴링 큐마다 해당 스케쥴러가 동작한다.

&nbsp;

**Short-term scheduler** , 혹은 **CPU scheduler** 는 

다음에 어떤 프로세스가 실행되고 CPU를 할당해야 할지를 정한다.

이 스케쥴러는 ready queue 에서 동작하며, ready 상태인 프로세스 중 하나를 고른다.

이는 작은 운영체제에서는 유일한 스케쥴러일 수도 있다.

이 스케쥴러는 time-sharing system 의 경우 밀리초 단위로 아주 빈번하게 실행된다.

&nbsp;

**Long-term scheduler** , 혹은 **job scheduler** 는

어떤 프로세스가 ready queue 로 와야 하는지를 정한다.

이는 new 상태의 프로세스를 ready 상태로 변경할지를 선택한다. 

여기서 선택받아 ready 가 된 프로세스는 정식으로 프로세스로서의 활동을 시작한다.

이렇게 되면 현재 실행 중인 프로세스로 카운트 되어,

degree of multiprogramming 에 포함된다.

즉 이 Long-term scheduler 는 degree of multiprogramming 의 증가 속도를 늘리는 역할을 한다.

이는 short-term 과 달리 몇 초에서 몇 분에 한 번 실행된다.

&nbsp;

프로세스는 행동하는 성향에 따라 

**I/O-bound process** 와 **CPU-bound process**로 구분된다.

&nbsp;

전자는 연산보다 I/O 처리하는데 시간이 더 걸려서, 

프로세스가 CPU를 사용하는 구간인 CPU burst 가 많고 짧다.

&nbsp;

후자는 I/O 보다는 연산 처리가 더 걸려서

CPU 버스트가 적고 길다.

&nbsp;

Long-term scheduler 는 이 두 종류의 프로세스가 적절하게 섞이게 해야 한다.

둘의 수가 적절한 비율을 유지해야 한다. 이는 Long-term scheduler 의 몫이라고 할 수 있다.

I/O-bound process 가 많아지면 ready queue 가 비어서 CPU scheduler 가 할 일이 없어지고,

CPU-bound process 가 많아지면 device queue, 특히 I/O 장치들의 큐가 비어서 장치들이 할 일이 없다.

&nbsp;

&nbsp;

## Medium-term scheduling의 추가

&nbsp;

**Medium-term scheduler** 는 degree of multiprogramming 이 줄어들어야 할 때 추가될 수 있다.

이는 일부 프로세스를 'swap device'라고 불리는 disk 의 영역으로 내쫓는 일을 한다.

시스템의 프로세스가 너무 많으면 main memory 에서도 여유가 없으므로

커널은 메인 메모리의 빈 공간을 확보하기  위해 일부 프로세스들을 디스크 공간으로 내보냈다가

프로세스의 수가 다시 줄어들면 그때 메모리로 불러들이는데, 이를 'swapping'이라고  한다.

&nbsp;

Long-term scheduler는 degree of multiprogramming 을 결코 줄이지 않는다.

degree 는 프로세스가 종료되면서 자연스레 감소하는 것이다.

Long-term scheduler 는 이것의 증가 속도를 올리지만

Medium-term scheduler 는 일시적으로 이 정도가 높을 때 조절하는 역할을 한다.

&nbsp;

<img src="https://user-images.githubusercontent.com/22045424/78582808-d1380080-7870-11ea-9ffd-febc8e6a0d16.png" width=70%>



&nbsp;

위 그림에서 ready queue 에서 출발해 CPU를 할당받은 프로세스는

I/O 가 필요한 단계에 접어들고, 그때 I/O를 기다리는 queue에 들어가고 I/O 장치를 사용한다.

I/O 가 끝나면 다시 ready queue 에 들어가게 된다.

&nbsp;

이때 I/O 를 요청하는 그 순간 프로세스는 waiting 상태가 되는데

이때 Medium-term scheduler 가 이를 swap device로 보낸다.

&nbsp;

waiting 상태의 프로세스는 메인 메모리에 있어 봤자 아무 일도 못하기 때문에 

swap 당하기에 적당한 후보라고 할 수 있다.

I/O 가 완료되면 다시 메인 메모리로 돌아와서 ready queue 에 머물게 된다.

&nbsp;

&nbsp;

## Context Switch

&nbsp;

Context Switch 는 프로세스 간 CPU의 사용 전환을 뜻한다.

이는 이전 프로세스 상태를 저장하고 다음 프로세스의 상태가 복원되는 과정을 포함한다.

프로세스의 Context 간 사용 중인 프로세스의 register 값, 메인 메모리 상의 내용을 통칭한다.

이는 PCB에 기록되어 있다.

&nbsp;

CPU가 다른 프로세스들로 전환될 때 시스템은 **context switch** 를 통해

이전 프로세스의 **상태를 저장** 하고

새 프로세스를 위해 저장된 **상태를 불러와야** 한다.

&nbsp;

프로세스의 **Context**  는 PCB 에 나타나있다. 

Context 는 다음으로 나뉠 수 있다.

* register context : register 의 값들
* memory context : 메모리 상의 이미지
* system context : PCB와 같은 자료구조

&nbsp;

context switch 는 사실 이 작업이 필요한 건 맞지만

이 작업 자체는 의미가 없는 작업이라 오버헤드가 생기게 된다.

따라서 가급적 짧고 신속하게 하는 것이 좋다.

너무 자주하면 안 되고, 한 번 할 때 짧게 하는 것이 좋다.

&nbsp;

요즘은 이 switch 를 빠르게 하는 CPU가 있다.

이는 명령 하나로 한꺼번에 상태를 저장하고 불러오는 기능이 추가된 것이다.
