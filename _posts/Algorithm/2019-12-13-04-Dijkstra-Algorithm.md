---
date : 2019-12-13
title : Dijkstra's Algorithm
categories : [Algorithm]
---

## 최단 거리 찾기

그래프에는 가중치가 있을 수 있다.  
이 가중치는 주로 노드 사이의 거리나, 비용 등을 의미한다.  

네비에서 길을 찾을 때나, 여행을 갈 때나  
최소비용, 최소시간, 최단거리로 길을 가는 것은 아주 중요하다.  

그리고 이를 BFS 를 통해서 찾을 수 있다.  

BFS 는 넓이 우선 탐색으로, 자연스레 자신으로부터 멀리 떨어진 노드를 가장 나중에 탐색한다.  
따라서 만일 각 엣지의 가중치가 같다면 한 노드 (BFS 시작지점) 로부터의 거리는 점점 증가하므로  
BFS 한 번으로 한 노드에서 다른 모든 노드까지의 거리를 구할 수 있다.  
하지만, 가중치가 제각각이라면 어떨까?


### 기본적인 개념  

path, 길의 정의 :  
v1 에서 u 로 가는 길이 v0, v1, v2... vk, u 라고 하자.  
이때 path 는 (v0, v1), (v1, v2) ... (vk, u) 이다.  
v0 에서 v1 까지 가는 길, v1 에서 v2 까지 ...  

path 의 비용 :  
각 path 에서 드는 비용의 총합이다.  
우리의 목적은 이 비용을 최소화하는데에 있다.  

간단한 구현으로, 각 엣지의 가중치-1만큼 그 사이에 노드를 두고  
모든 엣지의 가중치가 1이라고 치고 계산할 수도 있다.  

A - B 사이의 가중치가 5라면 A 와 B 사이에 빈 노드를 넣어서  
A-O-O-O-O-B 로 바꿀 수 있다.  

이후 BFS 한 번만 하면 모든 노드까지의 최단 거리를 구할 수 있다.  

물론 이 방법은 시간, 공간이 너무 낭비된다.  



### 알람 클록

가중치를 시간이라고 치자.  

　A  
B　C  

삼각형의 그래프가 있다.  
B-A, B-C, A-C의 가중치는 각각 100, 200, 50 이다.  

이때 B에서 C로 가려고 하는데,  
200 만큼의 시간이 걸리므로 B-C 구간의 도착 알람을 200 만큼 맞춰놓자.  

동시에 B 에서 A 로도 가보는데, 이때는 B-A 도착의 알람이 100으로 설정되었다.  
이때 A에 도착해 알람이 울린 후, A 에서 C 로 갈 수 있다는 사실을 알게 되었다.  
그리고 A-C 구간의 시간은 50이므로,  
B 에서 A 도달 후 C 를 가도  
기존의 B-C 구간 알림인 200 보단 더 빨리 울리게 된다.  
따라서 우리는 B-C 구간의 알림을 200이 아니라 150으로 재설정해야 한다.  

이렇게 B 에서 C 의 최단거리를 구할 수 있었다.  

그리고 이 아이디어를 구체화해서 알고리즘으로 만들어 보자.  


## 다익스트라 알고리즘 (Dijkstra's Algorithm)

다익스트라 알고리즘은  
한 곳에서 다른 모든 곳까지의 최단 거리를 구한다.  
방향이 있는 그래프에서 사용되며, (없어도 안 되진 않는데, path 라는 측면에서 방향이 없으면 의미가 없다.)  
가중치엔 음수가 있어선 안 된다.  

다익스트라는 위에서의 알람 클록을 매번 재설정하는 것이다.  

![image](https://user-images.githubusercontent.com/22045424/70800111-bf78ac00-1dee-11ea-896a-f95d33ff98a2.png)

A 노드에서 모든 노드까지의 거리를 구할 것이다.  

초기 세팅 :  
자기 자신까지의 거리는 0 이다.  
그리고 이미 B 와 C 는 연결이 되어 있으므로, 알람 클록을 맞출 수 있다.  
아직 연결이 되어 있지 않아 모르는 노드까지의 거리는 무한대(INF)로 두자.  

|A|B|C|D|E|
|:---:|:---:|:---:|:---:|:---:|
|0|4|2|INF|INF|


1단계 :  
A가 갈 수 있는 노드 중 가장 가까운 (최소의 가중치를 가지는)  
C를 방문해본다.  
이때 C와 연결된 노드는 B, D, E 인데,  
A-C 의 거리 2와 C-B 의 거리 1의 합은 3, (현재 A-B는 4)  
마찬가지로 A-C, C-D 는 6이고, (현재 A-D는 INF)  
E까지는 7이다. (현재 A-E는 INF)  

위 셋은 현재 A-B, A-D, A-E 보다 작다.  
따라서 알람 클록 때 했던 것처럼 거리를 수정해주자.  

|A|B|C|D|E|
|:---:|:---:|:---:|:---:|:---:|
|0|3|2|6|7|  


2단계 :  
(이미 간 C를 제외한) A가 갈 수 있는 노드 중 가장 가까운 B를 방문해본다.   
B와 연결된 노드는 C, D, E로  
현재 A-C는 2이나, A-B + B-C 는 6이므로 수정하지 않는다.  
그러나 D는 현재 6이지만,  
A-B가 3, B-D가 2로 현재 A-D 인 6보다 작으므로 이를 둘의 합인 5로 수정하자.  
같은 이유로 A-E 도 6으로 수정한다.  

|A|B|C|D|E|
|:---:|:---:|:---:|:---:|:---:|
|0|3|2|5|6|

3단계 :  
이번에는 D 를 방문해야 할 것이다.  
D 에서 갈 수 있는 노드는 없다.  

변경 없음.  

4단계 :  
마지막으로 E를 방문하지만,  
역시 변경되는 부분은 없다.  

최종적으로 A부터 다른 노드까지의 거리는  

|A|B|C|D|E|
|:---:|:---:|:---:|:---:|:---:|
|0|3|2|5|6|

이 된다.  

### 시간 복잡도 분석  

다익스트라 알고리즘은  

1. 노드 수만큼 노드를 방문하며  
2. 방문할 때마다 가중치가 최소가 되는 노드를 찾는다.  
3. 그리고 두 가중치의 합과 현재 출발지로부터의 가중치를 비교한다.  

1에서 n 번 실행  
2에서 최소를 비교하면서 역시 노드만큼 n 번 실행하므로  

시간 복잡도는 O(n^2) 이다.  


그러나 그 최소값을 찾는 것을 피보나치 힙으로 구현한다면  

O(nlogn) 이 걸릴 것이다.  

### 코드

[GitHub](https://github.com/jkjan/Algorithm/blob/master/Graph%20Algorithm/05.%20Dijkstra%20Algorithm/Dijkstra%20Algorithm.cpp)
