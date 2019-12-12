---
title: "Data Transfer Instructions 3"
date: 2019-11-26 08:26:28 -0400
---

### 3. Type Conversion Instructions

1. cbw
2. cwde
3. cwd
4. cdq

1. cbw  
convert byte to word 의 줄임말이다.  
이는 AL 을 sign extension 을 하여 
AX 로 만든다. (word 크기 = 2 byte = 16 bit)

2. cwde  
convert word to double word extended 의 줄임말이다.  
AX 를 sign extension 하여 
EAX 로 만든다.

3. cwd   
convert double word 의 줄임말이다.  
AX에 있는 걸 DX : AX 로 나눈다.  

4. cdq  
convert double word to quad word 의 줄임말이다.  
EAX에 있던 걸 EDX : EAX 로 나눈다.  


위 넷은 operand 따위 없고 무조건 A 레지스터만을 바꾼다.  
특징은, cwd와 cdq 는 나눗셈 명령인 div 를 염두에 둔 명령어이다.  
AX에 있는 값을 나누고 싶다면, cwd 를 한 번 쓰고 나누어야 한다.  
반대로 EAX 에 있는 값을 나누고 싶다면 cdq 를 쓰고 나눈다.  
  
  
  
movsx, movzx 명령어.  
movsx 는 mov 하돼, sign extend 를 하고 옮기라는 뜻이다.  
```nasm
movsx cx, bl
```
위 명령어는 bl 에 있는 값을 sign extension 한 뒤, cx 에 저장한다.  
결과적으론 cbw 와 같은 일을 한다.  

movzx 는 sign extension 이 아닌 zero extension 이다.  
```nasm
movzx eax, DATA2
``` 
위 명령어는 DATA2 에 있는 값을 zero extension 한 후, eax 에 저장한다.  
