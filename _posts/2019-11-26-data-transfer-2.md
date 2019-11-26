---
title: "Data Transfer Instruction 2"
date: 2019-11-26 08:26:28 -0400
---

## 3. Stack Manipulation

`push`, `pop`, `pusha`, `popa` 명령어는  
데이터를 스택으로, 스택을 데이터로 옮긴다.  

1. push  
push 16 혹은 32비트 레지스터, 상수, 세그먼트 레지스터,  
혹은 word나 double word 크기의 메모리 데이터를 연산자로 가진다.  

2. pop  
위 push 와 같이, CS(Code Segment) 레지스터를 제외한  
모든 값을 연산자로 가진다.  

세그먼트의 구성  

|stack|
|:---:|
|heap|
|data|
|code|

Stack Segment 는 운영체제에 의해서 알아서 관리된다.  


# push 의 동작

push 명령어는 Stack Segment 에 operand로 들어온 값을 삽입한다.  
이때 ESP값이 줄어들어 항상 ESP가 stack segment 의 top을 가리키게 한다.  
```nasm
push  eax
```
EAX의 32비트가 스택의 Top 으로 간다.  
  
operand 를 가지지 않는 push 명령어도 있다.  
바로 pusha 이다. (Pusha - T의 그 Pusha가 아닌 것 같다.)  
pusha 는 operand 를 가지지 않는다. 그대신, 이때 push 가 되는 레지스터는 순서가 정해져있다.  

|EAX|
|:---:|
|ECX|
|EDX|
|EBX|
|old ESP|
|EBP|
|ESI|
|EDI|   <--- ESP  

  
여기서 old ESP 는 그때의 push 직전의 ESP 의 복사본이다.  
  
  
# pop 의 동작

pop 은 push 와 반대로 ESP 가 증가하는 방향이다.  
pop ECX 라 함은, 현재 ESP가 가리키고 있는 곳(스택의 탑)의 값이  
ECX 로 저장된다는 뜻이다. 

그렇다면 popa 는?  
위의 pusha 순서와 반대로 pop 을 진행한다.  
단, old ESP 는 무시하고 지나간다.  
애초에 이걸 다시 ESP에 복사한다면 굉장히 큰 문제가 생긴다.  
  
  
  
## Address Loading Instructions

`lea` : 명령어 주소지정 방식이 결정한 데이터의 주소를 32비트 레지스터에 로드한다.

`lds` 와 `les` : 48비트 메모리 주소를 32비트 오프셋 주소와 ds 혹은 es 에 로드한다.

`lfs`. `lgs`, `lss` : 48비트 메모리 주소를 32비트 오프셋 주소와 fs, gs, 혹은 ss 에 로드한다.

(여기서 ds, es, fs, gs, ss 는 segment register 다.)  
`lea` 는 오른쪽 인자가 결정한 주소를 왼쪽 인자에 저장한다.  
`lea EAX, [EBX + ECX*4 + 100]` 는  
EAX에 EBX에서 ECX * 4 + 100 만큼 더 간 주소에 담긴 값을 EAX에 저장한다는 의미이다.  

lea vs mov 
lea 는 주소를 복사한다.  
mov 는 값을 복사한다.  
