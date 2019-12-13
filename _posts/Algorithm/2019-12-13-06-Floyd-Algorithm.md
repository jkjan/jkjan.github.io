---
date : 2019-12-13
title : Floyd's Algorithm
categories : [Algorithm]
---

가중치가 다 똑같은 그래프에서 BFS 로 최단거리를 찾든  
다익스트라를 쓰든, 벨만포드를 쓰든  
다 한 가지 노드에서 모든 노드까지의 최단거리를 찾는 방식이다.  

모든 노드에서 다른 모든 노드까지의 최단거리를 찾아보자.  

## 플로이드-와셜 알고리즘 (Floyd-Warshall's Algorithm)

행렬로 구현된 그래프를 필요로 한다.  
(인접 리스트로 구현된 그래프로 플로이드 알고리즘을 구현해봤는데  
더럽기만 하고 행렬로 구현한 거랑 별 차이 없을 것이다.)  

음의 가중치는 허용되나, 음의 사이클은 허용되지 않는다.  


### 기본 개념 
Ak[i][j] :  
i 부터 j 까지, 사이에 k의 노드를 추가했을 때의 최단 거리이다.  


A-1[i][j] :  
i 부터 j 까지의 엣지의 가중치이다.  

이때 Ak[i][j]는 Ak-1[i][j] 와 Ak-1[i][k] + Ak-1[k][j] 의 최소값이라는 아이디어로 출발한다.  
즉 i와 j 사이에 1번 노드, 2번 노드, 3번 노드 ... 를 계속해서 끼워넣는 것이다.  

## 코드 

```c++
void Floyd ( int cost[][], int dist[][], int n ) {
  int i, j, k;
  for ( i = 0; i < n; i++ )  
    for ( j = 0; j < n; j++ )
      dist[i][j] = cost[i][j];

  for ( k = 0; k < n; k++ ) {  
    for ( i = 0; i < n; i++ )
      for ( j = 0; j < n; j++ )
        if ( dist[i][k] + dist[k][j] < dist[i][j] )
          dist[i][j] = dist[i][k] + dist[k][j];
  }
}
```

dist 라는 n * n 짜리, i행 j 열은 i부터 j 까지의 최단거리를 나타내는 행렬을  
모든 노드의 쌍에 대하여 (2, 3번째 for문) n번 수정해나가는 함수이다. (맨 밖의 for문)  

시간복잡도는 O(n^3) 이다.  

