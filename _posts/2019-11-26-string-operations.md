---
title: "String Operations"
date: 2019-11-26 08:26:28 -0400
---

### String Operations

`movs`, `lods`, `stos`, `ins`, `outs` 등이 있다.  
바이트, 워드, 혹은 더블 워드 크기의, 혹은 이들의 반복된다면 이들의 각 블록을 전송한다.  
D flag (direction) 와 esi, edi 가 쓰인다.  

DF 가 0이라면 edi 와 esi 를 증가시킨다.  
DF 를 0으로 만드려면 `cld` 라는 명령어를 쓰자.  
DF 가 1이라면 edi 와 esi 를 감소시킨다.  
DF 를 1로 만드려면 `std` 라는 명령어를 쓰자.  

edi :  
extra segment 에 있는 데이터에 접근한다. (override 할 수 없다.)  

esi :  
data segment 에 있는 데이터에 접근한다. 
segment 를 override 한다는 접두사가 있으면 override 가 가능하다.  



# lods 명령어
data segment 에서부터 현재 esi 가 갖고 있는 값만큼 떨어진 곳에 있는 데이터를  
al, ax 혹은 eax 으로 불러온다.  

```nasm
lodsb``` 는
data segment 의 esi 가 가리키고 있는 바이트 단위 string 을 a 레지스터로 가져오라는 뜻이다.  

```nasm
es  lodsb DATA1
```
과 같이 예외적으로 extra segment 에서도 가져올 수 있다.  
  
  
  
# stos 명령어  
이는 앞서 설명한 `lods` 와 반대로 작동한다.  
extra segment 에서 edi 만큼 떨어진 곳에 현재 a 레지스터를 저장한다.  
복사가 일어난 후 edi 는 자동으로 증가한다.  
  
  
  
# movs 명령어  
extra segment 에서 edi 만큼 떨어진 곳에 data segment 에서 esi 만큼 떨어진 곳의 데이터를 저장한다.  
movsb, movsd 가 있다. 각각 byte 단위, double word 단위의 스트링이다.  
복사가 일어난 후 edi 는 증가, esi 는 감소한다.  
  
  
  
# rep 명령어
위의 stos, movs 명령어는 하나만 움직이므로 이걸 반복해주어야 한다.  
왜냐면 당연하게도 string 명령어는 반복 실행을 가정하고 있다. (lods 제외)

```nasm
mov edi, 0
mov ecx, 25*80
mov eax, 0720H
rep stosw
```
는 
extra segment 에다가 0720H 을 25*80 번 반복해서 저장하는 코드가 된다.
