---
date : 2019-12-19
title : Multistage Graph
categories : [Algorithm]
---

## Multistage Graph

### Multistage Graph 의 정의 

방향 그래프이다. (A directed graph)  

버텍스들은 1<=i<=k 에서 Vi 집합에 해당하는 k>=2 개의 부분으로 나뉜다.  
만일 u에서 v까지 가는 엣지 <u, v> 가 E 에 속할 때  
u는 Vi, v는 Vi+1에 속한다.  

|V1| = |Vk| = 1이다.  

s 가 V1 에 속한다면 s 는 source 이다.  
반대로 t 가 Vk에 속한다면 t 는 sink 이다.  

c(i, j) 가 엣지 <i, j> 의 비용이라고 생각해보자.  

s 부터 t 까지의 경로의 비용은 그 경로에 놓인 비용들의 합이다.  
multistage graph 문제는 s에서 t까지 가는 경로의 최소 비용을 구하는 것이다.  

![multistage](https://user-images.githubusercontent.com/22045424/71179098-dae22c00-22b2-11ea-8664-078fe80f7307.png)


### 전략 

P(i, j) 를 Vi 의 버텍스 j 에서 sink 까지의 최소 비용이 들게 하는 경로라고 하자.  
이때 COST(i, j) = min(c(j, l) + COST(i+1, l)) 이다.  
단, l은 Vi+1 에 속함.  

이때 P(1,1) 과 COST(1,1) 을 구하면 된다.  

방식은 weighted interval scheduling 했던 것과 같이 메모이제이션과 재귀함수와 동일하다.  



### 적용

자원 할당 문제  

r 개의 프로젝트에 n 개의 자원을 할당하자.  
i프로젝트에 j 개를 할당할 때 발생하는 이익을 N(i, j) 라 하자.  
이익을 최대화하는 방법은?  
이 문제를 멀티스테이지 그래프로 풀어보자.  

r+1 스테이지의 그래프 문제로 바꾸자.  
Stage i 는 프로젝트 i 를 말한다.  
n+1 개의 버텍스 V(i, j)는 stage i 와 결합한다.  
Stage 1 과 Stage r+1 은 단 하나의 버텍스를 가진다.  
V(i, j) 버텍스는 프로젝트 1, 2,.. i-1 에 할당해온 자원들의 수 j를 가리킨다.  
엣지는 <V(i, j), V(i+1, l)> 이다. 이때 비용은 N(i, l-j) 이다.  

