---
title : CPU Switch From Process to Process
categories : [Operating System]
date : 2020-04-07
---

&nbsp;

##  프로세스 간 CPU 전환

&nbsp;

<img src="https://user-images.githubusercontent.com/22045424/78555206-7f2eb500-7847-11ea-88c2-7eb657a833db.png" width=30%>

&nbsp;

위는 이전 글에서의 프로세스 제어 블록 (Process Control Block, PCB)

Multi-Programming 에서는 process 간 CPU 사용이 전환되기 때문에  

위 사진에서의 Program Counter나 Registers 같은 정보가 필요하다. 

&nbsp;

Multi-Programming 에서는 CPU 의 idle 상태를 막고

활용성을 극대화로 끌어올리기 위하여

프로세스 간 CPU 의 사용을 전환한다.  

&nbsp;

<img src="https://user-images.githubusercontent.com/22045424/78579361-c62ea180-786b-11ea-8d1c-49afd7e858a3.png" width=50%>

&nbsp;

위 그림은 프로세스 P_0와 P_1 사이에서 CPU 의 사용이 전환되는 과정을 나타낸다.

&nbsp;

P_0 -> P_1 로의 전환이 이루어지려면

P_0 의 사용중이던 레지스터 값을 P_0의 PCB에 저장해야 한다.

P_1 도 과거에 했던 곳부터 다시 실행을 재개하려면 멈추기 직전의 레지스터 저장값이 필요한데,

이는 P_1의 PCB에 있다.

&nbsp;

P_0 이 실행되는 도중 입출력의 사용 등의 interrupt나 system call 이 발생하면

제어가 커널로 넘어가게 되고, P_0은 실행을 중단해야 한다.

커널은 P_0의 PCB인 PCB_0에 P_0 의 현재 상태를 저장한다.

다시 말해 프로그램 카운터와 현재 CPU 안의 레지스터가 메모리 상의 PCB로 복사된다.

&nbsp;

이후 커널은 P_1을 실행하기 위해 PCB_1에 있는 값을 CPU의 레지스터에 복사한다.

CPU의 레지스터에 복사하면 CPU의 Program Counter register 값이 바뀌게 되며

새로운 PC 값으로 실행의 분기가 발생하여

CPU가 P_1로 실행이 전환된다.

&nbsp;

P_1도 실행을 하다가 interrupt 나 시스템 콜이 발생하면 PCB_1에 그때의 상태를 저장하고

다시 P_0을 실행하기 위해 P_0의 상태를 복원하고

P_0의 실행이 재개된다.

&nbsp;

이때 그림의 맨 위에 있는 PCB_0 에 있는 값들과 맨 밑의 PCB_0 값들은 동일하다.

&nbsp;

주의할 것은 P_0가 P_1로, 다시 P_0으로 CPU 사용이 전환되기 위해선

P_0 -> 커널 -> P_1 -> 커널 -> P_0 의 과정을 거친다는 것이다.

즉 **프로세스 간의 실행 전환 중간에는 항상 커널이 한 번씩 실행된다.**

&nbsp;

&nbsp;

## Linux 에서의 프로세스 표현

&nbsp;

`task_struct`라는 C 구조체로 표현될 수 있다.

&nbsp;

<img src="https://user-images.githubusercontent.com/22045424/78580294-2a9e3080-786d-11ea-8b57-2a62a76e7954.png" width=70%>

&nbsp;

위에서부터

* 프로세스의 아이디
* 프로세스의 상태
* 프로세스 스케쥴링에 필요한 정보
* 현재 PCB의 프로세스의 부모 프로세스 PCB의 포인터
* 이 프로세스의 자식 프로세스의 PCB 리스트의 앞 주소
* 프로세스가 사용 중인 파일 리스트
* 프로세스가 메모리 상 사용 중인 공간에 대한 정보

&nbsp;

<img src="https://user-images.githubusercontent.com/22045424/78580609-9b454d00-786d-11ea-96f2-75d1d59edaa9.png" width=70%>

&nbsp;

PCB 들은 이중연결리스트로 구현이 되어 있다.

리스트 중 현재 실행 중인 PCB를 가리키는 `current` 포인터가 존재한다.

&nbsp;

&nbsp;
