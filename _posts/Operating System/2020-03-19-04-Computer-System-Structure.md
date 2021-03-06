---
title : Computer System Architecture  
categories : [Operating System]  
date : 2020-03-19  
---  


# I/O Structure  

CPU와 I/O 장치는 독립적인 관계로 일을 하며  
I/O 장치가 작업을 마치면 CPU에게 인터럽트 신호를 보내기로 되어있다.  
이때 CPU가 일을 하는 방식에는 두 가지 방식이 있다.  

첫번째는 CPU가 입출력을 시작하고 나서  
일을 끝냈다는 인터럽트가 올 때까지 기다리고 있는 것이다.  
idle 상태 (idle은 사전에서 나태한, 게으르다는 뜻을 가지고 있다.)에 진입하여  
그때의 명령만 되풀이하면서 입출력 장치가 일을 끝마칠 때까지 오매불망 기다리고 있다.  
즉 한 번에 하나의 입출력만이 가능한 방식이다.  

두번째는 명령만 내리고 자기 일을 하는 것이다.  
인터럽트가 오면 그때 해당 입출력을 처리한다.  
이 방식은 입출력 장치의 상태와 유형, 주소를 가지고 있는 Device-status table 을 가지고 있다.  


# Direct Memory Access Structure  

보조 저장 장치에 있는 응용 프로그램은  
실행 시에 그 일부가 메모리에 올라가고  
그 메모리에서 실행되고 있는 순간의 명령어와 데이터가 CPU에 이동하고   
입/출력을 할 상황이 오면 입출력 장치에게 명령한다.  

많은 데이터가 입출력 되는 상황에  
이 일을 다 CPU가 관여하고, CPU가 인터럽트 받을 때마다 일을 중지하면  
굉장히 비효율적이게 된다.  
따라서 CPU - 입출력 장치 - 메모리 - 입/출력을 반복하는 일을   
입출력 장치 - 메모리로 바로 이동하게 하는 기능이다.  

이렇게 하면 고속의 저장 장치에서 바로 메모리에 접근하는 속도로 입출력 장치에 접근할 수 있다.  
데이터도 일일히 가는 것도 아니고 블록 단위로 보내진다.  

입력 장치의 Device Controller는 CPU의 명령을 거치지 않고 (CPU가 굳이 입력 장치를 가동시키지 않아도)  
buffer에서 바로 메인 메모리로 블록 단위의 데이터를 전송한다.   
출력 장치도 역시 CPU가 따로 안 말해줘도 메모리에서 바로 데이터를 가져와 출력한다.  
이 구조로, CPU가 스타트만 끊어주면 입출력 장치와 메모리가 알아서 일을 하게 된다.  


# 현대의 컴퓨터가 작동하는 원리  

![image](https://user-images.githubusercontent.com/22045424/77051988-83696e80-6a0f-11ea-8929-18faf5707e7e.png)  

0. 기억 장치에 저장된 프로그램(데이터와 이를 이용하는 명령어들)은  
실행 시 일부 메인 메모리로 이동한다.  

1. 메인 메모리에 올라온 데이터는 명령어의 실행(process)을 위해  
CPU - Central Processing Unit에 올라간다. (fetch됨)  - instruction execute cycle   
이때 이 메모리 - CPU 의 데이터 전송속도는 아주 느리므로,  
이전에 사용되었던 데이터 혹은 명령은 CPU 옆의 Cache에 저장한다.  

2. 사용자로부터 입력을 받거나 사용자에게 출력해야 할 경우 입출력 장치를 개시한다.  
(I/O request)  

3. 입출력이 완료되면 CPU에게 신호를 보낸다. (Interrupt)  
혹은 CPU를 방해하지 않고 바로 메모리와 상호작용한다. DMA (Direct Memory Access)  

이는 캐시와 DAM만 빼면 1945년 폰 노이만이라는 25살에 교수가 된 지구 역사상 최고의 천재가 제안한 구조로  
75년이 지난 지금도 컴퓨터는 이 구조로 개발되고 있다.  



# 컴퓨터 시스템의 구조

대부분의 시스템은 하나의 범용 프로세서를 사용한다.  

멀티프로세서(Multiprocessor) 시스템은 점점 더 많아지고 있고, 더 중요해지고 있다.  
이는 병렬 시스템, 멀티 코어 시스템이라고 불린다.  
이의 장점은  
1. 처리량의 증가 (프로세서가 많으므로. 그러나 n개가 있다고 처리량이 n배가 되진 않음)  
2. 경제적 이점  
3. 신뢰도 향상 (저하나 에러가 나도 버틸 수 있음)  
등이 있다.  

CPU 중 하나가 주로 사용되고, 그 CPU가 나머지 CPU들에게 명령을 내리는 구조인 (프로그램이 1번 CPU에게만 할당)  
비대칭형 멀티프로세싱(Asymmetric Multiprocessing)과  
이와 반대로 모두가 동등하게 일처리를 할 수 있는 (프로그램이 1번 CPU, 2번 CPU에도 갈 수 있음)  
대칭형 멀티프로세싱이(Symmetric Multiprocessing)이 있다.  

또한 CPU 마다 메모리 접근 시간이 균일한 UMA (Uniform Memory Access)   
그렇지 않은 NUMA (Non Uniform Memory Access) 등이 있다.  

# A Dual-Core Design

이 여러개의 프로세스들을 각각 따로 부품을 만들지 않고  
한 칩에 여러 프로세스를 둘 수 있다.  
일단 칩-칩 보다 칩 안에서 서로 의사소통을 하는 것이 더 빠르고   
전력 소모도 적다.  

이 중 blade server는 여러 개의 처리, 입출력, 네트워크 보드를 가지는 시스템으로  
각 보드는 독립적으로 부팅되고, OS도 각기 다르게 설치될 수도 있는 시스템이다.  

이와 비슷한 방식 (여러 개의 머리)으로  
Clustered Systems 를 들 수 있는데  
이는 대신에 프로세서가 여러개인 것이 아니라  
여러개의 시스템이 같이 일을 하는 것이다.  

컴퓨터들이 LAN이나 네트워크로 연결되어  
SAN(Storage Area Network)으로 같은 저장 공간을 공유한다.  
이 역시도 구조에 따라서  
Asymmetric clustering 과 Symmetric Clustering 으로 나뉠 수 있다.  

일부 클러스터는 고성능의 컴퓨팅 환경을 위해 사용된다. (High-performance computing)  
