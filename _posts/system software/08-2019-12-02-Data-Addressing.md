---
title: "Data Addressing Modes"
date: 2019-12-02 08:26:28 -0400
---

### Data Addressing Modes

`mov`와 `movs` 등이 있다.  
알다시피, `mov`는 reg/reg, reg/mem 끼리만 가능하다.  
mem/mem 이 가능한 명령어는 string 명령어인 `movs`이다.  
또한, 대부분의 데이터 전송 명령어들은 EFLAGS 를 바꾸지 않는다.  

## 4-바이트 데이터 너비
a 라는 주소가 n 바이트라고 표시가 되어 있으면,  
참조되는 메모리는 a, a+1, a+n-1 이다.  

n 바이트 수가 메모리에 저장되어 있다면,  
이 바이트들은 중요한 순서대로 저장이 되어 있다. -> 리틀 엔디안  


# Register  
`mov eax, ebx`   
레지스터의 주소 지정 방식  
주의 : 두 레지스터의 크기는 같아야 한다.
`mov eax, bx`  
이런 건 허용되지 않는다.  
mov 중 그 어떤 명령어도 EFLAGS 를 건드리지 않는다.


# Immediate  
`mov ch, 0x4b`  
ch라는 곳에 4b를 바로 저장하는 방식이다.  
명령어에 상수의 형태로 연산자가 주어진다.  
b는 이진수, o는 8진수, h는 16진수이다. 아무것도 없으면 십진수이다.  
`mov eax, 'A'` 이런 것도 가능하다.  

# Direct (eax) Displacement (다른 레지스터들)  
`mov [0x4321], eax`  
주소를 직접 지정해서, 그 번지수에 해당하는 메모리에 eax를 직접 저장한다.  
이는 세그먼트에 정의된 변수(directive)를 메모리 주소의 베이스로 삼는 방식.  
`mov eax [data1]` 이라고 하면 data1 이 가리키는 바로 그곳의 값을 eax에 저장한다.  

반대로,  
`mov eax data1` 이라고 하면 data1 이라고 하는 주소가 들어간다.  

Direct 와 Displacement 의 차이는,  
A 레지스터와 함께 mov 가 일어나면 Direct 라고 하고,    
그 외 레지스터는 Displacement 라고 부른다.  


# Register Indirect  
`mov [ebx], cl`  
현재 데이터 세그먼트 + ebx 만큼 간 주소의 메모리에 cl 의 값을 저장한다.  
displacement와 방식은 비슷하나, displacement 는 특정 label 로 하는 반면,  
이는 레지스터를 base로 한다.  
즉, 레지스터에 넣는 값에 따라서 지정할 수 있는 주소가 달라진다.  
```nasm
mov al, [edi]
mov [edi], 0x10
mov byte [edi], 0x10
```
모두 가능하다.  
단, esp는 사용될 수 없다.  
어떤 크기가 들어있을지 모를 때에는 byte, dword 같은 prefix 를 사용한다.  


# Base-plus-index  
`mov [ebx + esi], ebp`  
데이터 세그먼트 + ebx + esi 만큼 간 주소의 메모리에 접근한다.  
베이스 레지스터와 인덱스 레지스터가 있다.  
베이스 인덱스는 배열의 시작 주소를 저장하고 있다. (ebp, esp)
인덱스 레지스터는 offset 를 저장하고 있다.  (edi, esi)
무엇을 base로, offset 둘 지는 어셈블러의 마음이다.  


# Register relative  
`mov cl, [ebx+4]`  
데이터 세그먼트 + ebx + 4 만큼 간 주소의 메모리에 접근한다.  
base + displacement 의 조합으로 사용이 가능하다.  
base 가 되는 레지스터는 주소의 시작점에 대한 정보를 저장하고 있다.  
그리고 그 주소로부터의 떨어진 정보는 displacement 가 정적으로 가지고 있다.  

(index * scale) + displacement   
scale 에 요소의 크기가 2, 4, 또는 8임에 따라 인덱스의 스케일을 조절할 수 있다.  


# Base Relative - plus - index addressing
세그먼트 베이스 + 베이스 + 인덱스 + 상수 로 주소를 지정한다.  
이는 2차원 배열의 주소를 지정하기 위해 고안됨.  
```nasm
mov dh, [ebx+edi+20H]
mov ax, [FILE + ebx + edi]
mov [LIST + ebp +esi + 4], dh
mov eax, [FILE + ebx + ecx + 2]
```

# scaled-index addressing
세그먼트 베이스 + 베이스 + 상수 * 인덱스 로 주소를 지정한다.  

2, 4, 8 바이트의 크기를 가진 원소들의 2차원 배열 인덱싱 하는 법 :  
```nasm
mov eax. [ebx+4*ecx]
mov [eax+2*edi=100H], cx
mov eax, [ARRAY+4*ecx]
```
