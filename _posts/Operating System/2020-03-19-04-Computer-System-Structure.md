---
title : Computer System Architecture  
categories : [Operating System]  
date : 2020-03-19  
---  

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
