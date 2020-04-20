---
title : Process Synchronization
categories : [Operating System]
date : 2020-04-21
---

## 프로세스 동기화 (Process Synchronization)

&nbsp;

### 배경

* 프로세스는 병렬적으로 실행된다.

* 한 프로세스가 입출력하는 동안 

  다른 프로세스는 병렬적으로 작업을 수행할 수 있다.

* time-shared system의 경우 정해진 시간 동안 작업을 하고

  다른 프로세스로 작업이 전환된다 (context switch)

* 즉, 멀티프로세스 시스템이라면 프로세스는 실행 도중 중단(interrupt 받음)되며

  수행을 부분적으로 완료하게 될 수도 있다.

* 공유되는 메모리에 병렬적으로 접근한다면, 프로세스들이 데이터를 공유한다면

  데이터의 일관성이 깨질 수도 있다.

* 따라서 이를 방지하기 위해 프로세스의 실행을 관리해야 한다.

* 데이터의 일관성을 유지하는 것엔 프로세스들이 순서대로 실행되도록 하는 메커니즘이 필요하다.

&nbsp;

&nbsp;

### 생산자-소비자 문제

이전의 생산자-소비자 문제 (공유 메모리의 동기화 부분)에서

데이터 수를 나타내는 counter 변수가 추가되었다고 가정.

생산자는 데이터를 bound-sized buffer에 넣고 counter 를 증가시키고

소비자는 이 buffer에서 데이터를 빼고 counter를 감소시킨다.

이 둘은 독립적으로, 병렬적으로 실행된다.

이때 이 둘은 counter 라는 변수를 공유하며

이 변수의 일관성 (consistency)가 깨지는 것을

**race condition** 이라고 한다.

&nbsp;

### Race Condition

counter 를 증가시키는 `counter++` 는 사실

&nbsp;

```c
register1 = counter;
register1 = register1 + 1;
counter = register1;
```

&nbsp;

와 같은 과정을 거친다.

counter 는 메모리에 있기 때문이다.

연산은 데이터에서 바로 하지 않고 

데이터에서 레지스터로 load,

레지스터에서 연산 후 데이터에 store 하는 과정을 거치기 때이다.

&nbsp;

counter 를 감소시키는 `counter--` 도 똑같은 과정을 거친다.

counter 가 처음에 5라고 했을 때

생산자가 `counter++` 를 실행하고 소비자가 `counter--` 한다면

마지막에 counter는 5여야 하지만

위 세 순서, 즉 여섯 단계가 섞여버리게 되면

생산자가 register1 에서 계산된 값을 아직 counter 에 저장하지 못했는데

소비자가 그 값에서 1을 빼버릴 수도 있기 때문에

최종적으로 counter가 4가 돼버릴 수가 있다.

이렇게 되면 data의 consistency 가 깨지게 된다.

&nbsp;

이 현상을 막으려면

한 번에 한 프로세스만 공유 변수에 접근할 수 있도록

즉 하나가 여기에 접근하는 동안엔 다른 프로세스가 접근하지 못하게 막아야 한다.

&nbsp;

&nbsp;

## Critical Section Problem

시스템이 n 개의 프로세스 {P0, P1, ... Pn-1} 을 사용한다고 가정하자.

각 프로세스는 **Critical Section** 을 가지고 있다.

이는 프로세스의 코드 중 공유 데이터에 접근하는 부분이다.

앞에서의 `counter++`, `counter--`에 해당한다.

즉 다른 프로세스가 이 부분을 수행하는 동안 다른 프로세스는 여기에 접근할 수가 없다.

이를 보장하는 방법을 만드는 것이 바로 **critical section problem** 이다.

&nbsp;

critical section 에 진입하기 바로 직전에 실행되는 부분을 **entry section**

직후 실행되는 부분을 **exit section** 이라고 한다.

나머지 부분은 **remainder section** 이라고 한다.

이 critical section problem 은 entry/exit section 을 고안하는 것이다.

&nbsp;

&nbsp;

## Critical Section 문제의 해결책

```c++
do {
    // entry section
    while (turn == j);
    
    /* 
    critical section
    */
    
    turn = j;
    
    /*
    remainder section
    */
}  while(true);
```

&nbsp;

위 예에서 turn 이 j 라면

critical section 에 접근이 불가능함.

turn 이 j가 아니어야 접근이 가능함.

&nbsp;

### 해결책의 세 가지 조건

&nbsp;

1. **Mutual Exclusion** 

   프로세스 Pi가 실행하는 도중엔 

   다른 프로세스는 critical section 을 실행할 수 없다는 게 보장되어야 함.

   

2. **Progress**

   critical section 을 실행 중인 프로세스가 없고 critical section 의 실행을 원하는 프로세스가 있다면

   어떤 프로세스가 그곳을 수행할 지를 정하는 것은 무기한 연기될 수가 없음.

   즉 remainder section 을 실행하는 프로세스는 

   critial section 에 들어가려는 프로세스에 영향을 주지 않으며

   누가 critical section 에 들어갈지는 현재 entry section 에 진입한 프로세스들 중에서만 가능함.

   

3. **Bounded  Waiting**

   프로세스는 entry section 에 유한번 진입 시도 후 critical section 에 진입 가능.

&nbsp;

2, 3번째 조건은 프로세스가 0이 아닌 속도로  진행된다는 것을 가정함.

단, 프로세스간 상대적인 속도는 예측할 수 없음.

P0이 P1보다 빨리 실행될 수도 느리게 실행될 수도 있음.

단지 0이 아닌 속도로 진행된다는 것만 가정할 수 있음.

&nbsp;

&nbsp;

## Peterson 의 해결책

두 개의 프로세스 간  가능한 해결책이고

순수한 S/W 적 해결책이다.

단 유일한 H/W 가정으로

기계어 명령어에서 `load`와 `store`가 원자적으로(방해 받을 수 없음) 실행된다고 가정함.

`int turn`, `Boolean flag[2]` 라는 공유 변수를 사용.

`turn`이라는 변수는 현재 critical section 에 누가 들어갈지를 나타냄.

`flag`는 프로세스 i가 들어갈 준비가 되었다면 `flag[i] = true` 가 되는 배열임.

&nbsp;

### P0 의 코드

```c++
do {
	// entry section
    flag[0] = true;
    
    // 자신보다 P1이 이곳에 접근하길 원한다면 진입할 수 있도록 보장 (Bounded Waiting)
	turn = 1; 
    
    // flag[1] = false 거나 (P1이 remainder 를 수행 중)
    // turn = 0 (P1이 P0에게 critical section 을 허락해줌)
    // 인 경우 loop 를 빠져나올 수 있음.
    while(flag[1] && turn == 1);
    
    /*
    critical section
    */
    
    flag[0] = false;
    
    /*
    remainder section
    */
    
} while(true);
```

&nbsp;

### P1 의 코드

```c
do {
	// entry section
    flag[1] = true;
	
    // turn 은 0과 1 둘 중 하나의 값을 가짐.
    // 두 개의 값을 가질 수 없으므로
    // turn = 0 / flag[1] = false
    // turn = 1 / flag[0] = false 두 경우 중 하나.
    turn = 0;
    
    // 다른 프로세스의 접근을 막는 것은 이것이 유일함.
    // 유일하다는 것으로 바로 progress 하다는 것이 증명됨.
    // 또한 대기 시간이 길어지지 않음.
    // P1 이 썼다면 turn = 0이 되므로 P0이 진입 가능하기 때문.
    while(flag[0] && turn == 0);
    
    /*
    critical section
    */
    
    flag[1] = false;
    
    /*
    remainder section
    */
    
} while(true);
```

&nbsp;

&nbsp;

&nbsp;

## 하드웨어를 통한 동기화

유니프로세서에선 인터럽트를 꺼서 가능함.

현재 실행 중인 코드를 방해없이 사용이 가능함.

critical section 에 진입하면 interrupt를 끄고 

벗어나면 다시 켜서 간단하게 mutual exclusion 이 가능함.

그러나 멀티프로세서 시스템에서는

이 interrupt를 껴고 끄려면 다른 프로세서들에게 모두 message 가 가야하므로

여기서 시간이 많이 걸려서 매우 비효율적임.

&nbsp;

현대의 컴퓨터는 메모리를 테스트하고 값을 set 하거나

두 메모리 워드의 값을 바꾸는 atomic 한 특별한 cpu 명령을 사용함.

&nbsp;

&nbsp;

### test_and_set 명령어

이는 메모리 특정 위치의 CPU 명령으로 

함수 형태가 아니나, 이해를 위해 함수처럼 표현된 것임.

&nbsp;

```c++
bool test_and_set (bool *target) {
    bool rv = *target;
    *target = true;
    return rv;
}
```

&nbsp;

코드를 잘 읽어보면

target 에 들어가는 값이 무조건 false 여야

이 함수가 false 를 반환하게 되어 있고

들어가는 순간 target 은 무조건 true 가 됨.

즉 false 값이 들어가면 일단 false 를 반환하지만 값은 true 가 됨.

&nbsp;

```c++
do {
    // 한 프로세스가 이를 실행 중이라면
    // lock 은 무조건 true임
    // 따라서 이 상태에서 다른 프로세스가 접근하면
    // test_and_set 은 true를 반환함.
    while(test_and_set(&lock))
        ;
    
    /* critical section */
    
    // lock을 해제하여
    // 이후 entry section  을 가장 먼저 실행하는 프로세스가
    // 진입 가능 판정을 받게 함.
    lock = false;
    
    /* remainder section */
    
} while(true);
```

&nbsp;

test는 값을 읽고 검사한다는 의미

set 은 해당 위치의 값을 1로 만든다는 의미임.

&nbsp;

### compare_and_swap 명령어

역시 CPU의 명령어이나 함수로 표현함.

&nbsp;

```c++
int compare_and_swap(int *value, int expected, int new_value) {
    int temp = *value;
    if (*value == expected)
        *value = new_value;
    // value 는 특정 메모리의 값이다.
    // value 를 읽음과 동시에 값을 바꿀 수도 있다.
    
    return temp;
}
```

&nbsp;

```c++
do {
    // lock 은 0으로 초기화.
    
    // 한 프로세스가 critical section 을 실행하는 도중에는 lock 은 무조건 1
    // 따라서 이는 true 를 반환하므로, 다른 프로세스들은 critical section 에 접근 x
    // lock 이 다시 0으로 돌아오면 맨 처음 이곳에 도달한 프로세스가 접근 권한 얻음.
    while (compare_and_swap(&lock, 0, 1) != 0);
    
    // critical section
    
    lock = 0;
    
    // remainder section
    
} while(true);
```

&nbsp;

&nbsp;

위 둘은 낮은 확률로 무한 루프를 돌 수 있음.

이는 해결책이 가져야 할 세 가지 조건 중

bounded waiting 에 어긋남.



## Bounded-waiting Mutual Exclusion with test_and_set

&nbsp;

`waiting[i] = true` 면 i번째 프로세스가 현재 Critical Section에 진입하길 원한다는 뜻.

모두 false 로 초기화.

`lock` 은 test_and_set 의 대상이 되는 변수. false로 초기화.

&nbsp;

```c++
do {
    // Pi 가 들어옴
    waiting[i] = true;
    
    // key 는 처음에 true이나
    // 누가 lock 을 풀어주면 false 가 될 수도?
    key = true;
    
    // key가 false 가 되거나 (lock 이 false)
    // 다른 프로세스가 critical section 실행 마친 후
    // 나의 waiting 을 true 로 바꿔주지 않는 이상
    // 접근 불가
    while(waiting[i] && key)
        key = test_and_set(&lock);
    
    // 나 이제 기다리는 프로세스 아님. 
    // 이제 critical section 을 실행할 거임.
   	waiting[i] = false;
    
    /* critical section */
    
    // 실행을 끝마치고 나서
    j = (i+1) % n;
    
    // 내 다음 프로세스들 다 보면서
    // waiting 이 true 인 애들을 찾음
    // n 으로 모듈러 연산하는 이유는 n-1 다음에 0으로 가기 위함.
    while((j != i) && !waiting[j])
        j = (j+1) % n;
    
    // 다 돌았는데 그 나온 이유가 j == i 라서
    // 즉 아무도 waiting 하고 있는 상태가 아니라면
    // lock 을 해제함. 
    // 나중에 entry section 으로 가는 프로세스는 
    // 누구든 바로 critical section 에 접근 가능 
    if (j == i)
        lock = false;
    
    // 누군가가 지금 critical section 에 들어가길 기다리고 있음
    // 나는 어차피 지금 critical section 실행을 끝마치고 오는 길이니
    // 길을 열어주기로 함.
    else
        waiting[j] = false;
    
    /* remainder section */
    
} while(true);
```

&nbsp;

한 프로세스가 나오면

다른 프로세스를 허락해주기 때문에

어느 프로세스라도 유한한 시간이 지나면

critical section에 진입이 가능함.

&nbsp;

&nbsp;

## Mutex Locks

위 작업들은 응용 프로그램에선 접근이 안 됨.

운영 체제는 이런 개발자들을 위해 이 critical section 문제의 해결책을 제시해야 함.

가장 간단한 Mutex Lock 이 있음.

Mutex Lock 은 `acquire()` 로 lock 을 열어 진입하고

`release`로 lock 을 해제하고 critical section 을 빠져나옴.

`acquire()` 는 반복문을  수행하는데

따라서 이는 **busy waiting** , **spilock** 이라고도 불림.

두 함수는 무조건 하드웨어적으로 원자성을 가짐.

&nbsp;

```c++
acquire() {
    while(!available)
        ; /* busy wait */
    available = false;
}
```

여기서 while 문은 available 이 false 인 동안 반복됨.

busy wait 은 단순 available 값만 테스트 하는 부분임.

&nbsp;

```c++
release() {
    available = true;
}
```

&nbsp;

```c++
do {
    acquire();
    
    /* critical section */
    
    release();
    
    /* remainder section */
} while(true);
```

&nbsp;

여기서 available 은 위 해결책들에서 보았던 lock 의 역할을 함.

false 로 초기화함.
