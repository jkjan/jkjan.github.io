---
date : 2019-12-19
title : Optimal Merge Pattern
categories : [Algorithm]
---

## Optimal Merge Pattern

### 문제 정의

이 문제는 k 개의 파일을 합치는 문제이다.  
k 개의 파일을 합치는 방식은 여러가지가 있다.  
n 개와 m 개의 레코드를 가진 정렬된 파일을 하나로 합치는데에 걸리는 시간은 O(n+m) 이다.  

k 개의 정렬된 파일들을 서로 합치는데, 이때 가장 최적의 방법을 구하라.  


### 예 :  
X1, X2, X3 의 정렬된 파일이 있다. 이들의 길이는 각각 30, 20, 10이다.  

이때 방법 1 은, X1 과 X2 로 Y1 생성 (50)  
그리고 Y1 과 X3 로 Y2 최종 생성 (60)  

방법 2는 X2와 X3 으로 Y1  (30)  
Y1 과 X1 로 Y2, (60)  

이들은 각 110, 90의 시간이 걸린다.  


### 해결 방법  

둘을 합치는 방법은 외부 경로(external path length)를 최소화 하는 이진 병합 트리이다.  

![merge](https://user-images.githubusercontent.com/22045424/71164013-96926400-2291-11ea-80b5-d7b448d4062b.png)

어떤 방법에서 드는 길이 qi 가 트리의 깊이 di 에 있을 때,  
그 패턴을 옮기는데 드는 시간은 qi * di 이고,  
총 걸리는 횟수는 1<=i<=n 까지 qi * di 의 합이다.  

이는 heap 구조를 이용한다.  
heap 에 합쳐야 할 파일들이 크기 순으로 정렬이 되어 있고,  
두 개를 pop 하여 얻어진 것들을 노드의 왼쪽 오른쪽으로,  
노드의 값(가중치)는 그 둘의 합으로 저장하고 그 값을 다시 heap 에 넣는다.  
이를 heap 이 빌 때까지 반복한다. (혹은 n-1번)  

그리고 이 시행이 끝난 후, root 에 있는 가중치가 바로 최적의 해이다.  



### 코드  

자료구조 :  

```c++
class Tree {
public:
	Tree* left;
	Tree* right;
	int cost;
	void init(Tree* left, Tree* right, int cost) {
		this->left = left;
		this->right = right;
		this->cost = cost;
	}
};

struct compare {
	bool operator()(Tree*a, Tree* b) {
		return a->cost > b->cost;
	}
};

typedef priority_queue< Tree*, vector<Tree*>, compare> Heap;
```


입력 :  

```c++
int input(Heap* files) {
	int n;
	int l;
	cin >> n;
	for (int i = 0; i < n; i++) {
		cin >> l;
		Tree* leaf = new Tree;
		leaf->init(NULL, NULL, l);
		files->push(leaf);
	}
	return n;
}
```

힙에서 pop 과 동시에 값 반환 :  

```c++
Tree* withdraw(Heap* files) {
	Tree* len = files->top();
	files->pop();
	return len;
}
```


트리 만들기 :  

```c++
void buildTree(int n, Heap* files, Tree* root) {
	for (int i = 0; i < n-1; i++) {
		Tree* tnode = new Tree;
		Tree* left = withdraw(files);
		Tree* right = withdraw(files);
		tnode->init(left, right, left->cost + right->cost);
		files->push(tnode);
		*root = *tnode;
	}
}
```


완성 코드 :  

```c++
#include <iostream>
#include <queue>

using namespace std;

class Tree {
public:
	Tree* left;
	Tree* right;
	int cost;
	void init(Tree* left, Tree* right, int cost) {
		this->left = left;
		this->right = right;
		this->cost = cost;
	}
};

struct compare {
	bool operator()(Tree*a, Tree* b) {
		return a->cost > b->cost;
	}
};

typedef priority_queue< Tree*, vector<Tree*>, compare> Heap;

int input(Heap* files);
void buildTree(int n, Heap* files, Tree* root);

int main() {
	Heap files;
	Tree root;
	int n = input(&files);
	buildTree(n, &files, &root);
	cout << root.cost;
}

int input(Heap* files) {
	int n;
	int l;
	cin >> n;
	for (int i = 0; i < n; i++) {
		cin >> l;
		Tree* leaf = new Tree;
		leaf->init(NULL, NULL, l);
		files->push(leaf);
	}
	return n;
}

Tree* withdraw(Heap* files) {
	Tree* len = files->top();
	files->pop();
	return len;
}

void buildTree(int n, Heap* files, Tree* root) {
	for (int i = 0; i < n-1; i++) {
		Tree* tnode = new Tree;
		Tree* left = withdraw(files);
		Tree* right = withdraw(files);
		tnode->init(left, right, left->cost + right->cost);
		files->push(tnode);
		*root = *tnode;
	}
}
```

입력값  

```text
5
5
10
30
20
30
```

으로 95라는 결과를 얻을 수 있다.  


