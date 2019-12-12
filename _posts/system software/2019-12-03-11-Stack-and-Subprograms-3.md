---
date : 2019-12-03
title : Stack and Subprograms 3
categories : [System Software]
---

## Enter 와 Leave 명령어

함수가 호출될 때, 
```nasm
push  ebp
mov   ebp, esp
sub   esp, LOCAL_BYTES

...

mov  esp, ebp
pop  ebp
```
와 같은 방식으로 
1. ebp의 저장 `push ebp`
2. esp의 저장 `mov ebp, esp`
3. 지역변수 할당 `sub esp, LOCAL_BYTES`  
~ 함수의 동작 ~
4. esp 복구 `mov esp, ebp`
5. ebp 복구 `pop ebp`

의 다섯 줄은 필수였다. 그러나 이제 1 ~ 3 을 enter,
4 ~ 5 를 leave 라는 명령어로 대체하자.

위 코드는

```nasm
subprogram :
            enter LOCAL_BYTES, 0
            
            ;
            
            leave
            ret         
```
으로 쉽게 대체된다. enter는 call frame 의 생성,
leave 는 call frame 의 제거이다.  


## Reentrant 와 recursive 한 subprogram

reentrant 한 subprogram 은 다음을 만족해야 한다. 
1. 그 어떤 코드 명령어도 바꿔선 안 된다.  
2. 지역변수를 바꿔서도 안 된다. (data와 bss 세그먼트에 있는 값)  
3. 모든 변수는 스택에 저장되어야 한다.  

reentrant 한 코드 작성의 장점
1. reentrant 한 subprogram 은 재귀적으로 호출될 수 있다.  
2. reentrant 한 프로그램은 여러 프로세스들이 쓸 수 있다.  
3. 멀티 스레드 프로그램에서 더 잘 돌아간다.  

reentrant 한 프로그램은 다중 프로세서, 다중 스레드에서  
그 함수를 공유할 때 문제가 없어야 한다.  


## 다중 모듈 프로그램

다중 모듈 (multi-module) 프로그램은 두 개 이상의 오브젝트 파일로 구성된다.  
모듈 A 가 모듈 B 에서 정의된 레이블을 쓰기 위해선 extern 이라는 directive 가 사용되어야 한다.  
레이블은 기본적으로 외부에서 접근할 수가 없다. 그러기 위해선 global 이란 directive 로, global 함이 선언되어야 한다.  


main.asm
```nasm
extern  get_int, print_sum

call  get_int
...
call  print_sum
```

sub.asm
```nasm
global get_int, print_sum

get_int : 
  ...
  
print_sum :
  ...
```

extern 이 없으면 main.asm 을 어셈블 할 때 get_int 가 무엇이냐 물어보는 에러가 난다.  
global 이 없으면 둘 다 각각 어셈블 할 때는 상관이 없는데,  
링크해서 실행 파일을 만들 때 main 쪽에서 찾아도 없다는 에러가 나온다.  

## C 와 어셈블리의 조우  

어셈블리의 루틴들은 C와 함께 쓰이기도 한다. 그 이유는  
- C 에서 접근이 어렵거나 불가능한 하드웨어 기능들에 직접 접근이 필요할 때가 있다.  
- 루틴이 가능한 한 빨라야 하고 프로그래머가 컴파일러보다 더 잘 최적화 할 수 있다.  

C 함수에서 어셈블리 부르기  

1. 레지스터 저장하기  
C는 함수가 다음 레지스터에 값을 저장하고 있을 것이라 예상한다 :  
EBX, ESI, EDI, EBP, CS, DS, SS, ES   

2. 함수의 레이블  
대부분의 C 컴파일러는 global, 혹은 static 한 함수에 언더바 하나('_') 를 앞에 붙인다.  

3. 매개변수 전달  
매개변수들은 함수 호출했을 때 보이는 것과 반대로 스택에 push 된다.  

4. 값 반환  
모든 정수형은 EAX 레지스터로 반환된다.  
만일 32비트보다 작다면, 32비트로 비트 확장된 다음 EAX 에 저장된다.  
64비트 값은 EDX:EAX 쌍으로 전달된다.  

5. 그 외  
cdecl 과 stdcall 방식이 있다.  
gcc 에서 다음과 같이 선언이 가능하다.  

```c
void f(int)_attribute ((cdecl))
```

대부분의 컴파일러는 cdecl 방식을 따른다.


## 커맨드 라인 매개변수

```c
int main (int argc, char[] argv) {
  if (argc != 3) {
    printf("wrong parameter form\n");
    return 0;
  }
  
  FILE* fp1 = fopen(*++argv);
  FILE* fp2 = fopen(*++argv);
  ...
```

위는 직접 작성한 리눅스의 cp 명령어의 일부이다.  
이를 컴파일 해서 실행파일로 만들 때  
gcc -o cp copy.c 로 하면 추후에
cp a.txt b.txt 로 바로 실행할 수가 있다.

이때 argc 는 매개변수의 개수 (실행파일 이름 포함)  
argv 는 매개변수 벡터의 시작지점을 갖고 있다.  

이전에 배운 스택 프레임을 그리자면,  

|Stack|
|:---:|
|argv[3]|
|argv[2]|
|argv[1]|
|argv[0]|
|...|
|pointer to argument vector|
|4|
|return address of main|
|old EBP|

가 된다.  
