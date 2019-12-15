---
date : 2019-12-15
title : Job Sequencing
categories : [Algorithm]
---

## 일의 순서 (마감기간)

- n 개의 일이 주어진다.  
- 일i 는 마감기간 di(>=0) 와 이익 pi(>=0) 를 가지고 있다.  
- 일을 마감기간 내에 하면 무슨 일이든 그에 맞는 이익을 얻는다.  

이번 문제는 일의 순서를 구하는 문제이다.  

일은 하나에 한 번만 가능하다.  
문제의 가능한 해는 일들 중에 마감기간에 끝낼 수 있는 일들의 부분집합 J 이다.  
그리고 그 J 에서 이익이 얻어진다.  
그 이익이 가장 크게 하는 집합을 찾자.  



### 예시

n = 4,  
(p1, p2, p3, p4) = (100, 10, 15, 27),  
(d1, d2, d3, d4) = (2, 1, 2, 1).  

이들의 가능한 해와 그때의 이익은?  


### 해결 전략  

- J 는 k 개 일들의 집합이며  
각 인덱스 i1, i2, ... ik 는  
그 인덱스에 해당하는 일의 데드라인보다 작거나 같아야 한다.  

- J 는 위의 원칙에 따라  
마감기간을 어기지 않으면서 할 수 있는 일들이어야지 최적의 해라 할 수 있다.  

- D(n) 을 n번째 일의 마감기간, J(n) 을 n번째 일이라고 할 때  
D(J(1)) <= D(J(2)) <= ... D(J(k)) 를 만족시켜야 한다.  

- 또한, D(J(r)) >= r 일 때, 1 <= r <= k 이다.  
r 번째 할 일의 마감기간은 r 보다 작아야 한다.  

- 이익을 내림차순으로 정렬한 후, 마감기간의 최소값이 1이라 가정한다.  

- 정렬된 일들을 실행하되, 앞의 일이 뒤의 일에게 양보할 수 있다면, 이를 양보하자.  


### 코드

일의 개수 n 이 입력되고  
그 다음 일의 이익, 마감 기간이 n 차례 입력됐을 때  
입력된 순서에 따라 일의 번호가 매겨지고,  
마감에 맞게 이익을 최대한 가져갈 수 있게 하는 일의 순서가 출력이 된다.  

```c++
class job {
public:
	int num;
	int p;
	int d;
	job() {}
	job(int num, int p, int d) {
		this->num = num;
		this->p = p;
		this->d = d;
	}
};
```

일의 번호, 이익, 마감을 저장하는 클래스
job 을 사용하였다.  

입력을 받는 부분이다.  

```c++
void input(int n, job* todo) {
	int p, d;
	for (int i = 1; i <= n; i++) {
		cin >> p >> d;
		todo[i] = job(i, p, d);
	}
}
```

일의 순서 리스트를 만드는 함수이다.  

```c++
int getJ(int n, job* todo, int* J) {
	sort(todo + 1, todo + n + 1, compare);
	int y;
	int cnt = 1;
	for (int i = 1; i <= n; i++) {
		y = 1;
		J[cnt] = i;
		while (y < i) {
			if (todo[J[y]].d > todo[J[cnt]].d) {
				swap(J+y, J+cnt);
			}
			y++;
		}
		if (todo[J[cnt]].d >= i)
			cnt++;
	}
	return cnt - 1;
}
```
for 문과 그 안의 while 문을 보자.  

일을 일단 추가하고, (1. solution)  
제약 조건인 마감기간에 맞춰야 하고 (2. feasible solution)  
최대한 많은 일을 해야 하므로 (3. objective function)  
앞에 있는 일들에게 양보를 요구하면서 (마감기간이 아직 남았으면, 뒤의 일을 먼저 처리하자)   
양보가 가능하다면 일의 순서를 바꾼다. (내 마감기간보다 더 멀다면)   
for 문이 끝나면 최적의 해가 J 배열에 저장될 것이다. (4. optimal solution)  

그리고 양보도 다 받을만큼 받고나서, 그게 조건에 맞는다면  
cnt 를 증가시킨다.  

만일 양보를 아예 못 받았는데 슬프게도 마감시간에 못 맞추는 일이라면  
cnt 는 늘어나지 않을 것이고  
그 다음 for 문에서 다음 i 에게 덮어씌워질 것이다.  
  
  
  
최종 코드이다.  


```c++
#include <iostream>
#include <algorithm>

using namespace std;

class job {
public:
	int num;
	int p;
	int d;
	job() {}
	job(int num, int p, int d) {
		this->num = num;
		this->p = p;
		this->d = d;
	}
};

void input(int n, job* todo);
void swap(int* a, int* b);
int getJ(int n, job* todo, int* J);
bool compare(job a, job b);

int main() {
	int n;
	int cnt;
	cin >> n;

	job* todo = new job[n + 1];
	int* J = new int[n + 1];

	input(n, todo);
	cnt = getJ(n, todo, J);

	for (int i = 1; i <= cnt; i++) {
		cout << todo[J[i]].num << ' ';
	}

	delete[] todo;
	delete[] J;
	return 0;
}

void input(int n, job* todo) {
	int p, d;
	for (int i = 1; i <= n; i++) {
		cin >> p >> d;
		todo[i] = job(i, p, d);
	}
}

void swap(int* a, int* b) {
	int temp = *a;
	*a = *b;
	*b = temp;
}

int getJ(int n, job* todo, int* J) {
	sort(todo + 1, todo + n + 1, compare);
	int y;
	int cnt = 1;
	for (int i = 1; i <= n; i++) {
		y = 1;
		J[cnt] = i;
		while (y < i) {
			if (todo[J[y]].d > todo[J[cnt]].d) {
				swap(J+y, J+cnt);
			}
			y++;
		}
		if (todo[J[cnt]].d >= i)
			cnt++;
	}
	return cnt - 1;
}

bool compare(job a, job b) {
	return a.p > b.p;
}
```


위의 완성된 코드에  

```text
5
20 3
15 4
10 1
5 2
1 5
```

다음의 입력값을 주면  

```text
3 4 1 2 5
```

의 순서를 얻을 수 있다.  
