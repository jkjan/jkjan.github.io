---
date : 2019-12-19
title : Dynamic Programming
categories : [Algorithm]
---

## 다이내믹 프로그래밍  

**다이내믹 프로그래밍의 핵심 아이디어 1**  

그리디 알고리즘에서 출발한다.  
선택의 순서를 구하는 알고리즘이다.  
그리디 알고리즘의 반례가 되기도 한다.  

![graph](https://user-images.githubusercontent.com/22045424/71175329-a23e5480-22aa-11ea-8aea-f77c562e081d.png)


예를 들어, 그래프에서 최단 경로를 구하는 방법으로 그리디 알고리즘을 쓰지 못한다.  
  
    
      
        
**다이내믹 프로그래밍의 핵심 아이디어 2**   

분할정복 알고리즘에서 출발한다.  
문제를 subproblem으로 나눈다.  
recusrive 하게 푼다.  

예를 들어, 위 그래프에서 A->F 까지 가는 경로 Cost(A->F) 는  
min(w(A->B) + Cost(B->F), w(A->C) + Cost(C->F)) 이다.  

Cost(B->F) 는 또 다시 min(w(B->D) + ...) 로, Cost(C->F) 역시 min(w(C->D)+...) 로 나뉠 것이다.  

이로서 이런 결정에 관한 트리를 만들 수가 있다.  



### 최적의 원칙 

Principle of optimality :  

Whatever the initial states and decisions are,   
the remaining decisions must constitute  
an optimal decision sequence with regard to the state resulting from the first decision.  

초기의 상태나 결정이 무엇이든 간에,  
남은 결정들은 항상 첫 결정에서 만들어지는 상태에 관한 최선의 결정으로 이루어져야 한다.  
즉, 어떤 선택을 하든 그 선택지에서 갈 수 있는 또다른 선택지에서도 항상 optimal 한 결정을 내려야 한다.  

=> 이는 점화식으로 이어진다.  

**두 가지 접근 방식 : **  

Forward Approach  
결정 xi 를 위한 형태는 최선의 결정 순서 xi+1, ... xn 에 대해 만들어진다.  

Backward Approach  
최선의 결정 순서 x1, ... xi-1 로 만들어진다.  


## 예1 : 0/1 Knapsack

이전에 다루었던 Knapsack problem 과 달리  
이는 담거나 안 담거나 둘 중 하나로 결정된다.  

그리디 알고리즘에선 p/w 의 비율을 구하고, 이를 정렬하여 많은 순부터 채워넣었다.  

그러나 이 방법을 여기서 쓰면 반례가 존재한다.  

### 전략

KNAP(1, n, M) 이라는 식을 세운다.  
이때 각각 문제의 번호, 배열의 크기, 남은 무게이다.  

만약 1번에서 내가 물건을 담지 않는다면, KNAP(1, n, M) = KNAP(2, n, M) 으로 간다.  
담는다면, KNAP(2, n, M-wi) 가 될 것이다.  

이를 재귀적으로 구현할 때,  
j단계에서 최적의 해를 통해 얻을 수 있는 값을 gj(y) = KNAP(j+1, n, y) 라고 하자.  

g0(M) 은 KNAP(1, n, M) 이다.  

g0(M) = max(g1(M), g1(M-w1) + p1),  
...  
gi(y) = max(gi+1(y), gi+1(y-wi+1) + pi+1)  

이다.  

이때 예외처리로  

gn-1(M') = M' >= wn ? pn : 0  
현재 남은 무게가 wn 보다 크다면 pn 을 반환, 아니라면 0을 반환  

gi(M') = 0, if M' < min(wi+1,...wn)   
남은 물건 중 뭘 담더라도 무게가 초과되거나  

gi(M') = 모든 이익의 합, if 다 담아도 초과하지 않을 때  

가 있다.  


시간복잡도는 역시 최악의 경우를 구하므로  
2^n 이 된다.  

