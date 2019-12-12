---
title: "nasm 흐름 제어"
date: 2019-11-18 08:26:28 -0400
categories : [System Software]
---

control transfer instructions.


흐름의 제어엔 크게 두 가지가 있다.

무조건 분기와 조건 분기.

무조건 분기의 예로는 jump와 call, return 등이 있다.

조건 분기는 우리가 익히 아는 if 문, 반복문에서의 jump를 행할지 말지를 정하는 부분이 되겠다.

그리고 추가적으로 fault나 exception이 발생했을 때의 software interrupt나,

키보드의 입력과 같은 hardware interrupt의 경우에도 분기가 발생한다.


무조건 분기, unconditional transfer의 jmp 명령어는

program control을 해당 주소로 옮긴다. 이의 immediate operand 로

code label 을 두어 주소를 확실히 지정할 수 있다. 혹은, 범용 레지스터로 직접 지정할 수도 있다.

중요한 것은 이때의 jump 하는 곳은 code segment 안에 있는 주소이다. data segment 로의 jump는 의미가 없다.

또한 jmp는 말 그대로 jump 만 할 뿐, 돌아오지 않는다. 따라서, 복귀를 어디로 할 지에 대한 기록은 하지 않는다는 점에서

call 과의 차이점이 있다.



jmp 명령어의 예 :
```nasm
      mov eax, 0
      mov ebx, 2
XYZ:  add eax, ebx
      jmp xyz
```
  
이 코드를 실행시키면,

`eax` 에 0을 저장, `ebx`에 2를 저장

`eax` 에 `ebx` 를 더하고

다시 그 위의 `add` 가 위치한 XYZ라는 code label 로 jump 하여

계속 `eax` 에 2를 더하는 무한 루프가 만들어진다.

위 경우는 예시이므로 code label 을 XYZ 라 정의하였지만,

실제 코드에선 그 label 에서 무슨 일을 하는지 쉽게 알 수 있는 이름을 정하도록 하자.




