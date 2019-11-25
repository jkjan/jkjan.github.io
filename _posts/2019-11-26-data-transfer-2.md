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

|---|---|
|stack|   |
|heap|   |
|data|   |
|code|   |

Stack Segment 는 운영체제에 의해서 알아서 관리된다.  
