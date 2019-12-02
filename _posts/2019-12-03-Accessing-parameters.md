---
date : 2019-12-03
title : Stack and subprogram 2
---

## Accessing Parameters with ESP

![esp](./esp.png)

```nasm
print_int : 
            push ebp
            mov ebp, esp
            ...
            ...
            pop ebp
```

함수가 call 되면 ebp 레지스터가 그 당시의 esp 값을 복사한다.  
단, ebp 레지스터는 범용 레지스터이기 때문에 호출 이전에 쓰였을 경우도 있으므로  
push ebp와, 마지막 pop ebp 를 통해 ebp 를 복사, 저장한다.  

ebp 에 들어있는 값은 바뀌지 않는다.  
ebp 는 스택의 기준 주소가 된다. 그래서 ebp 에 index 를 더해서  
parameter 에 접근한다.  

![ebp](./ebp.png)


## 일반적인 함수 호출자와 피호출자의 형태와 매개변수 전달  

호출자 :  
```nasm
push  dword 1     ; 1을 매개변수로 전달
call  subprogram  
add   esp, 4      ; 스택에서 매개변수를 제거
```

여기서 마지막 줄의 `add esp, 4` 는 stack 에서 매개변수를 pop 하는 동작이다.    
pop 하면 되지만, 사실 함수가 종료된 후 매개변수는 필요 없기 때문에,  
어디다 저장하지 않고 esp 만 증가시켜준다.  


피호출자 :  
```nasm
subprogram : 
            push ebp          ; 원래의 ebp 값을 스택에 저장한다.
            mov ebp, esp      ; 새 ebp 에 esp 값을 저장한다.
            
            ; subprogram code
            
            pop ebp           ; 원래 ebp 값을 복구한다.
            ret
```

push ebp 동작을 통해  
이전에 뭘 했든 복구가 된다.  



## 스택에 올라가는 지역변수

스택은 지역 변수의 위치로도 사용될 수 있다.  
스택은 C 프로그램이 일반 (automatic) 변수들을 저장하는 바로 그곳.  
subprogram 을 reentrant 하게 만들기 위함.  
지역 변수는 필요한 바이트만큼 ESP 에서 빼는 방식으로 저장.  
EBP 의 값이 스택에 들어간 직후 저장이 된다.  
EBP 레지스터를 베이스로 한 indirect addressing 으로 접근한다.  

esp - n byte 으로 지역변수를 할당한다.  
즉 할당 후 ebp - m byte 로 지역변수에 접근한다. (n>=m)   

이러한 설계 때문에 같은 함수를 또 부르더라도  
두번째, 세번째 함수가 쌓여도 첫번째 함수가 쓰던 매개변수는 남아있다.  

따라서 함수의 재귀호출이 가능하다. 이를 reentrant 하다고 한다.  



# 정리

```c
main() {
  ...
  i = func(1, 2);
  ...
}
```

```c
int func(int x, int y) {
  int a, b, c;
  ...
  return c;
}
```

```nasm
...
push  dword 2
push  dword 1
call  func
add esp, 8
```

```nasm
func :
       push ebp             ; 원래의 ebp 값을 저장
       mov  ebp, esp        ; 새로운 ebp 에 esp 저장
       sub  esp, 12         ; 지역변수로 사용할 바이트 할당
            
       ; subprogram code
            
       mov  eax, [ebp-12]  ; 리턴값 전달
       mov  esp, ebp       ; 지역변수 해제
       pop  ebp            ; 원래 ebp 복구
       ret
```


위에서의 스택 프레임은  

|ESP + 24|EBP + 12|2|
|:---:|:---:|:---:|
|ESP + 20|EBP + 8|1|
|ESP + 16|EBP + 4|Return address|
|ESP + 12|EBP|Saved EBP|
|ESP + 8|EBP - 4|Local var a|
|ESP + 4|EBP - 8|Local var b|
|ESP|EBP - 12|Local var c|

가 된다. 또한, 이런 frame 은 함수 호출될 때 생겼다가 함수가 끝나면 사라진다.  
함수가 중첩되면, 이 프레임이 계속 생기는 것이다. 밑에 계속해서 생긴다.(reentrant)   
따라서, 함수가 제대로 종료되지 않고 계속 쌓이면 Stack Segment 를 넘어가게 되는 일이 발생.  
이렇게 되면 Stack Overflow 가 난다. 그래서 Call 과 return 의 균형이 맞아야 한다.  



