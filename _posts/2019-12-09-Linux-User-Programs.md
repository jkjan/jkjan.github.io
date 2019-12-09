---
date : 2019-12-09
title : Linux User Programs
---

### 멀티태스킹에서의 메모리 변환
가상주소 -> 선형주소 - 물리주소

가상주소를 선형주소로 바꾸기 위해선 segmentation 이 필요하고,  
선형주소를 물리주소로 바꾸기 위해선 paging 작업이 필요하다.  
일부 운영체제는 paging 을 하지 않아, 선형주소에서 따로 물리주소로 바뀌지 않는다.   

** segmentation 의 필요성  
segment 내의 index, offset 을 통해 주소를 지정하나,  
그 segment 가 정확히 어떤 주소에 있는지 (base) 를 알지 못하기 때문에  
이를 운영체제가 segmentation 을 통해 linear address 로 바꾸어 주어야 한다.  
  
** paging 작업  
그리고 이를 약 4kb 단위의 page 로 쪼개서 RAM에 넣는다.  
현재 프로그램 실행에 필요한 페이지만 RAM에 올라가므로,  
프로그램의 페이지가 100개라 하여 100개 모두가 RAM 을 차지하지 않는다.  


### 멀티 태스킹

- 프로그램들이 프로세스를 차례대로 이용하게 한다.  
- 스케쥴러는 프로그램이 끝나지 않았더라도 다른 프로그램에 양보하도록 할 수 있다.  
- 업무의 상태를 기록하는 세그먼트로, 충돌한 프로세스는 그 현재 상태를  
  메모리의 일부분에 기록한다.  


### 메모리 보호

- 프로그램들의 메모리 주소 충돌을 막기 위함이다.  
- 멀티태스킹은 메모리 보호를 필요로 한다.  
- 리눅스는 이를 위해 페이징(paging)을 사용한다.  
- 사용자 프로그램에의 모든 메모리 참조는 메모리의 접근 요청을 통해 이루어지며  
  승인을 받기 위해선 페이징 시스템을 거쳐야 한다.  
  
  
## Protected Mode
CR0 레지스터의 0번째 값을 clear 하면  
Protected mode 가 활성화된다.  

Protected mode 에선  
- 모든 메모리 참조는 global descriptor table 에 의존한다.  
- 명령어들은 다른 세그먼트에서 fetch 된다.  
- interrupt table 의 각 entry 들은 8바이트의 entry 로 특별하게 정해져 있다.  
  (즉, 이 모드로 접근하기 전에 새 interrupt table 이 만들어진다.)
  
Global Descriptor Table 이란, segmentation 과정에서  
segment 들의 주소를 linear address 로 바꾸는데 필요한 자료구조이다.  
실제 segment 들의 주소를 저장하고 있다.  


### Protected Mode 에서의 Segmentation  
Segment Register 는 Descriptor Table 의 어느 위치에  
자기가 가르킬 Segment의 주소가 있는지를 저장한다.  
그래서 이를 Segment Selector 라고 한다.  

Segment Selector 엔 다양한 값들이 저장되어 있다.  

- Base : 세그먼트의 첫 바이트의 주소를 저장한다.  
- Limit : offset 주소의 마지막으로 유효한 값  
- Access Permission : 읽기, 쓰기, 실행할 권한을 표시한다.   
- Access Privilege Level : 세그먼트의 소유권  

권한의 예로,  
코드 세그먼트는 읽기 권한이 있으나 실행 중 쓰기 권한이 존재하지 않다.  
데이터 세그먼트는 실행 권한이 존재하지 않는다.  

소유권의 예로,  
커널 프로그램은 다른 프로그램들의 세그먼트에 접근할 수 있다.  
하지만 커널이 아닌 일반 사용자 프로그램은 다른 프로그램의 세그먼트에 접근할 수 없다.  


## GDT, LDT

GDT : Global Descriptor Table  
LDT : Local Desciptor Table  

GDT 는 세그먼트들의 위치를 저장하는 테이블이다.  
LDT 는 그 세그먼트 안에서 Code Segment, Stack Segment 들의 위치를 저장한다.  

즉 세그먼트의 위치를 저장하는 것은  
GDT 를 루트로 시작해 LDT1, LDT2...  
그리고 그 LDT1 이 각각의 Code, Stack, Data Segment 를 가리키게 하는  
트리 구조이다.  
LDT 는 프로그램의 개수만큼 생성이 된다.  
n 개의 프로그램이 있다면 이는 n개가 된다.  
