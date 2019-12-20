---
date : 2019-12-19
title : Weighted Interval Scheduling
categories : [Algorithm]
---

## (Weighted) Interval Scheduling 

처리해야 할 일의 집합이 주어진다.  
각 일은 시작 시간과 끝나는 시간이 있다.  
두 일이 겹치지 않으면 이는 호환이 된다. (compatible)  
목표는 호환이 되는 부분집합의 최대를 구하는 것이다.  


### 만일 그리디라면

그리디 알고리즘으로 이 문제를 풀면 어떻게 될까  

1. 회의가 일찍 시작하는대로 선택해보자.  

　ㅡ　ㅡ　ㅡ　ㅡ  
 ㅡㅡㅡㅡㅡㅡㅡㅡ   
 
 위의 경우라면?  
 밑에 있는 것 단 하나밖에 선택하지 못할 것이다.  
 
 
2. 회의가 가장 짧은 것을 택한다면  
 
 ㅡㅡㅡㅡ　ㅡㅡㅡㅡ  
 　　　ㅡㅡㅡ  
 
 위의 경우엔 옳은 선택을 못할 것이다.  
 
 
3. 제일 안 겹치는 일만 택하자.  
 
 그렇다면  
 
ㅡㅡㅡ　ㅡㅡㅡ　ㅡㅡㅡ　ㅡㅡㅡ  
　　ㅡㅡㅡ　ㅡㅡㅡ　ㅡㅡㅡ  
  　ㅡㅡㅡ　　　　　ㅡㅡㅡ  
    ㅡㅡㅡ　　　　　ㅡㅡㅡ  
    
위 경우에선 가운데에 맨 밑의 딱 하나만 선택하게 될 것이다.  

4. 일찍 끝나는 순으로 정해야 한다.  




### 확장  

만일 일에 가중치가 있다고 생각해보자.  

그렇다면 위의 그리디 알고리즘으로 이를 해결할 수 있을까?  

각 일은 시작 시간, 끝나는 시간, 가중치로 입력이 된다.  

이때의 목표는 가중치의 합이 최대가 되게 하는 호환되는 집합을 구하는 것이다. (a set of compatible intervals)  


### 전략 

interval 들을 끝나는 시간에 따른 내림차순으로 정렬한다.  

각 간격 j 에 따라 p(j)를 구한다.  

p(j) 는 간격 i 와 만나지 않는 i < j 인 가장 큰 인덱스이다.  


![jobs](https://user-images.githubusercontent.com/22045424/71177785-17f8ef00-22b0-11ea-87b7-1a351726ff56.png)

이떄 해결책은  

OPT(j) = max (vj + OPT(p(j)), OPT(j-1) ) 이다.  
(vj 는 j 번째 일의 가중치)  



위 식을 풀면  

OPT(6) = max{1 + OPT(3), OPT(5)}  
OPT(5) = max{2 + OPT(3), OPT(4)}  
OPT(4) = max{7 + OPT(0), OPT(3)}  
OPT(3) = max{4 + OPT(1), OPT(2)}  
OPT(2) = max{4 + OPT(0), OPT(1)}  
OPT(1) = max{2 + OPT(0), OPT(0)}  

이고, 이를 코드로 구현하면  

```c++
int OPT(int j) {
  return (j==0 ? 0 : max(v[j] + OPT(P[j]), OPT(j-1)));
}
```

단 한 줄로 끝난다.  

### 성능 향상

위의 6줄의 식을 보면  
당장 OPT(3) 은 3번이나 계산이 되고 있다.  
따라서 2번이나 헛계산을 하고 있는 것이다.  

따라서 우리는 이를 저장하는 M 이라는 배열을 만들어 주자.  

```c++
int OPT(int j) {
  if (j == 0)
    return 0;
  if (M[j] != -1)
    return M[j];
  M[j] = max(v[j] + OPT(P[j]), OPT(j-1));
  return M[j];
}
```

아니면 재귀가 아니어도  

```c++
int OPT (int j) {
  int M[];

  M[0] = 0;

  for ( i = 1; i <= n; i++ )
    M[i] = max( v[i] + M[P[i]], M[i-1] );
}
```
로도 해답을 구할 수 있다.  

이 해결방법은 Backward Approach 이다.  
시간복잡도는 O(n) 이다.  

여기서의 특징은  
메모이제이션, 보조 메모리이다.  
