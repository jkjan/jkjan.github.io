---
title : Interprocess Communication
categories : [Operating System]
date : 2020-04-07

---

&nbsp;

## 프로세스 간 통신 (IPC)

&nbsp;

프로세스는 기본적으로 **independent** 하다.

한 프로세스가 다른 프로세스의 실행에 영향을 주거나 받지 않는다.

&nbsp;

때로는  **cooperating** (협업) 할 수 있는데

이때는 다른 프로세스의 실행에 영향을 주고 받는다.

&nbsp;

프로세스는 다음과 같은 이유로 협력한다.

* 정보 공유
* 작업을 나누어 실행해 연산 속도를 올리기 위함
* 모듈화된 시스템을 여러 프로세스로 나누어 실행
* 한 사용자가 편리함을 위해 여러개 실행

&nbsp;

협력하는 프로세스 (cooperating process) 는 **interprocess communication (IPC)** 가 필요하다. 

IPC는 **shared memory** 와 **message passing** 의 두 가지 모델이 있다.

&nbsp;

&nbsp;

## IPC - Shared Memory

&nbsp;

<img src="https://user-images.githubusercontent.com/22045424/78587563-16abfc00-7878-11ea-9bfd-85f34adcdee9.png" width=30%>

&nbsp;

통신하고자 하는 프로세스가 일정한 메모리를 공유하여 데이터를 주고 받는다.

여기서는 프로세스 A와 프로세스 B가 메모리를 공유하고 있다.

각 프로세스는 이  메모리 공간을 자기 자신의 주소 공간의 일부로 간주한다.

&nbsp;

A가 공유 메모리 영역에 데이터를 쓰고 B가 읽음으로써 데이터 전달 효과를 볼 수 있다.

별도의 송신, 수신 함수가 필요 없다. 그냥 읽고 쓰는 게 송수신이다.

단, A가 먼저 쓰고 그 다음에 B가 읽어야 데이터 전송이 되는 것인데, 

이를 컨트롤 하는 것은 커널이 아닌 사용자 프로세스라서 이 순서가 제대로 되리라는 보장이 없다.

이는 공유 메모리 모델의 큰 이슈 중 하나로 통신을 위해 공유 메모리를 접근할 때 

서로의 동작을 동기화하는 메커니즘이 필요하다.

&nbsp;

&nbsp;

## 생산자-소비자 문제

&nbsp;

이는 프로세스 간 동기화 개념을 설명하는데 있어 자주 이야기 되는 고전적인 문제이다.

두 개의 프로세스는 생산자와 소비자 역할을 맡고 있다.

이들은 buffer 라고 하는 공간을 공유한다.

생산자는 데이터를  생성하여 buffer 에 저장하고,

소비자는 그것을 꺼내서 소비한다.

이는 생산자가 송신하고 소비자가 수신하는 통신 문제로도 볼 수 있다.

&nbsp;

**unbounded-buffer** 는 버퍼의 크기에 한계가 없다.

**bounded-buffer** 는 고정된 크기의 버퍼를 사용한다.

&nbsp;

&nbsp;

## Bounded-Buffer - shared-memory 해결법

&nbsp;

공유 메모리에서 이 생산자-소비자 문제를 해결하는 방법이다.

&nbsp;

생산자와 소비자가 공유하게 될 버퍼에 대한 정의이다.

```c
#define BUFFER_SIZE 10
typedef struct {
    ...
} item;
// 자신이 원하는 데이터를 구조체로 정의한다.

item_buffer[BUFFER_SIZE]; // 실제 버퍼 정의, 고정된 크기이다

int in = 0;
int out = 0;
/*
buffer 의 특정 위치를 가리키는 pointer 역할을 한다.
in 은 생산자가 생성할 데이터를 저장할 곳
out 은 소비자가 꺼내갈 곳을 의미한다.
처음에는 버퍼가 비어있으므로 0으로 초기화한다.
*/

```

&nbsp;

<img src="https://user-images.githubusercontent.com/22045424/78588316-5b846280-7879-11ea-96ec-9355d56391dd.png" width=30%>

&nbsp;

버퍼는 다음과 같이 첫번째 칸과 마지막 칸이 이어져있는

circular list 이다.

&nbsp;

마지막 칸에서 한 칸 더 이동하면 다시 0으로 되돌아간다.

버퍼는 순차적으로 접근한다.

in을 1씩 증가시키며 데이터를 넣고

out을 1씩 증가시키며 데이터를 꺼낸다.

in은 항상 out보다 앞서있어야 한다.

**현재 buffer에 있는 데이터는 in과 out 사이에 있는 (in - out) 개의 데이터이다.**

&nbsp;

위 그림에서의 in은 4, out 은 0이다.

따라서 데이터는 4 - 0, 4개이다.

&nbsp;

생성자는 데이터를 생산해서 buffer에 넣는 과정을 반복하는 일을 한다.

&nbsp;

### 생산자

```c
item next_produced; // 생산한 데이터 임시 보관하는 변수

while (true) {
    /* produce an item */
    
    while (((in+1)%BUFFER_SIZE) ==  out)
        ; /* do nothing */ 
    /*
    위 while 문은 bounded buffer 의 상태를 살피는 반복문이다.
    buffer 에는 현재 빈 공간이 남아있어야 한다.
    없다면, 소비자가 데이터를 빼가서 빈 공간이 생기기를 기다려야 한다.
    생산자가 올바른 실행을 위해서 자신의 실행을 지연시키는 부분이며
    이것이 동기화 부분이다.
    */
    
   	buffer[in] = next_produced;
    
    /* 
    while 조건이 false 가 되어 반복문을 빠져 나왔다면
    실제로 데이터를 송신하게 된다.
    이는 이미 빈 공간이 있음을확인한 단계이다.
    */
    
    in = (int + 1) % BUFFER_SIZE;
    // 다음 작업을 위해 in 포인터를 갱신한다.
}
```

&nbsp;

### 소비자

```c
item next_consumed; // 소비할 데이터를 임시 보관

while (true) {
    while (in == out)
        ; /* do nothing */
    /*
    데이터를 꺼내기 전에 현재 bounded buffer를 살피는 부분이다.
    최소한 하나 이상의 데이터가 있어야 한다. 
    비어있다면 생산자가 데이터를 채워넣을 때까지 기다려야 한다.
    이는 생산자-소비자 문제 해결법의 핵심 부분이다.
    소비자가 올바른 수신을 위해 자신의 실행을 지연하는,
    실행을 동기화하는 부분이다.
    */
    next_consumed = buffer[out];
    // 수신. 꺼낼 수 있는 데이터가 하나라도 있다는 뜻이다.
    
    out = (out + 1) % BUFFER_SIZE;
    
    /* consume the item */
}
```

&nbsp;

&nbsp;

## 프로세스 간 통신 - 메시지 전달

&nbsp;

<img src="https://user-images.githubusercontent.com/22045424/78616048-19294880-78ae-11ea-8583-5cc659951a44.png" width=30%>

&nbsp;

메모리를 공유하는 방법 이외에 

프로세스 간 메시지를 전달하여 통신하는 방법도 있다.

위 그림은 메시지 큐를 이용한 방식이다.

커널 내에는 프로세스 A와 B가 통신 중 주고 받은 메시지가 저장된다.

프로세스가 송신하려면 커널의 메시지 큐에 저장하고, 수신은 그 반대로 거기서 꺼내어 사용하면 된다.

이는 사용자 공간의 메모리를 공유하는 방식과는 다르다.

메모리가 커널 내에 있고, 메시지 큐로 이어져 있는 것이다.

&nbsp;

IPC는 이 방식에서 두 가지 연산인 `send(message)`와 `receive(message)` 기능을 제공한다.

&nbsp;

메시지 크기는 고정이거나 가변적일 수 있다.

&nbsp;

&nbsp;

## 직접 통신 (direct communication)

&nbsp;

통신을 주고 받기에 앞서 서로를 가리킬 수 있는 방법이 있어야 한다.

직접 통신은 통신을 원하는 프로세스가 통신의 수신자, 송신자의 이름을 명시하는 통신 방법이다.

&nbsp;

이 방법은 송수신할 대상을 지정하도록 되어 있어

`send(P, message)` - P에게 메시지를 보냄, 

`receive(Q, message)` - Q로부터 메시지를 받음 의 함수를 가진다.

&nbsp;

이 communication link 의 성질로

* 링크가 자동으로 형성된다

  (매번 send, receive 할 때마다 상대방을 지정하기 때문)

* 링크가 오직 두 프로세스 간에서만 이루어지고

  둘 사이에 여러개를 연결할 수 없다

* 링크는 단방향일 수 있으나 대개 양방향이다

&nbsp;

라는 것들이 있다.

&nbsp;

&nbsp;

## 간접 통신 (indirect communication)

&nbsp;

메시지들이 우편함(mailbox)로 가고, mailbox에서 온다. (항구-port라고도 불린다)

각 mailbox는 자신만의 id가 있다.

프로세스들은 하나의 mailbox를 공유해야만 서로 통신할 수 있다.

&nbsp;

이 communication link 의 성질로

* 프로세스들이 공통의 mailbox를 공유해야만 link가 형성된다
* link는 많은 프로세스 간 형성될 수 있다
* 각 프로세스 쌍 사이엔 여러 communication link 가 있을 수 있다
* 단방향이거나 양방향이다

&nbsp;

라는 것들이 있다.

&nbsp;

간접 통신은 다음과 같은 연산을 수행한다.

* mailbox(혹은 port) 생성
* mailbox를 통해 메시지 주고 받기
* mailbox 제거

&nbsp;

기초적으로 다음과 같이 

`send(A, message)` - A라는 mailbox에 메시지 보냄

`receive(A, message)` - A라는 mailbox에서 메시지 가져옴

와 같은 동작이 있다.

&nbsp;

이때 P_1, P_2, P_3 의 세 프로세스가 A라는 mailbox를 공유하고

P_1 이 메시지를 보내고 이를 P_2, P_3 가 받을 수 있다고 할 때

둘 중 누가 받을까에 대해 생각해볼 수 있다.

&nbsp;

이에는 다음과 같은 세 가지 경우를 생각할 수 있다.

1. 한 연결에 최대 두 프로세스만 관여하도록 되어있다면 

   P_1이 누구와 연결되어 있느냐에 따라 달라진다.

   

2. 한 번에 한 프로세스만 receive 할 수 있다면 

   P_2와 P_3 중 누가 먼저 receive 에 성공하는지에 따라 실제 수신이 결정된다.

   

3. 시스템이 임의로 수신할 프로세스를 선택하는 경우로, 

   이 경우엔 나중에 sender 에게 P_2와 P_3 중 누가 수신자가 되었는지를 알려줘야 한다.

&nbsp;

&nbsp;

## 동기화 (synchronization)

&nbsp;

메시지 전달은 blocking 이거나 non-blocking 방식일 수 있다.



**blocking** 은 **동기적(synchronous)** 이라고도 한다.

blocking send 에서 sender가 메시지를 보내면, 

그 메시지가 전달된 것이 확인될 때까지 프로세스는 대기 상태가 된다. (blocked)

&nbsp;

blocking receive 는 마찬가지로 receiver는 메시지가 도착할 때까지 기다린다.

메시지가 도착하지 않았는데 receive를 호출한다면 이는 메시지가 올 때까지 기다린다.

&nbsp;

&nbsp;



**non-blocking** 은 **비동기적(asynchronous)** 이라고도 한다.

non-blocking send는 sender가 메시지를 보내고 나서 수신 여부에 관계 없이 일을 계속한다.

즉 이는 호출하고 나서 바로 return 이 가능하다.

여기서 send 는 수신자가 메시지를 받았는지 안 받았는지는 신경 쓰지 않는다.

무조건 보내고 받았든 안 받았든 return 한다.

&nbsp;

non-blocking receive 는 아직 메시지가 도착을 안 했으면

NULL 을 받았다고 하고 바로 return 한다.

&nbsp;

&nbsp;

서로 다른 조합이 가능하다.

특히 blocking send와 blocking receive 의 조합은 rendezvous 라고 한다.

&nbsp;

&nbsp;

## 메시지 패싱을 이용한 생산자-소비자 문제의 해결책



### 생산자

```c
message next_produced;
while (true) {
    // produce an item in next produced
    send(next_produced);
}
```

&nbsp;

위 코드는 메시지를 생산 후, `send()` 함수로 이를 보내는 코드이다.

&nbsp;

### 소비자

```c
message next_consumed;
while (true) {
    receive(next_consumed);
    // consume the item in next consumed
}
```

&nbsp;

위 코드는 `receive()` 함수로 메시지를 받아 이를 소비하는 코드이다.

&nbsp;

메시지 패싱에서는 생산자-소비자 관계가 별 거 없어진다.

공유 메모리 (shared memory) 에서는 동기화가 매우 중요했으나

메시지 패싱에는 non-blocking 방식과 같은 비동기적인 방법도 있기 때문이다.

&nbsp;

&nbsp;

## Buffering

&nbsp;

직접 통신, 간접 통신에 관계 없이 (direct or indirect communication)

communication link 마다 메시지들을 위한 큐 (message queue)가 있으며

프로세스들이 주고 받는 메시지는 이 임시 큐에 머물게 된다.

이런 메시지 큐를 구현하는 방법에는 다음의 3가지 방법이 있다.



1. zero capacity 

   queue의 길이가 최대 0인 것이다. 링크는 중간에 대기하는 메시지를 가질 수 없다.

   sender 는 receive가 메시지를 받을 때까지 무조건 기다려야 한다.

   이는 rendezvous 방식이기도 하다. (blocking send - blocking receive)

   

2. bounded capacity

   최대 n개의 메시지를 들 수 있다.

   sender 가 메시지를 보내고 바로 일을 할 수 있다.

   큐에 넣기만 하고 제 갈 길을 갈 수 있다.

   그러나 큐가 가득찬 경우 sender는 큐가 비기를 기다려야 한다.

   

3. unbounded capacity

   큐의 길이에 제약이 없다.

   이 방식으로 큐가 구현된 경우 sender 는 기다릴 경우가 없다.
