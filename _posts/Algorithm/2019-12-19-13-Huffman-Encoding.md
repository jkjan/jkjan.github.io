---
date : 2019-12-19
title : Huffman Encoding
categories : [Algorithm]
---

## Huffman Encoding

### 인코딩

MP3 의 인코딩 과정은 다음과 같다.  

일정 비율로 샘플링을 한다. 그 예로, CD는 초당 44,100번의 샘플링을 한다.  
이 기준 50분 길이의 교향곡은 50 * 60 * 44,100 = 130,000,000 의 샘플로 이루어진다.  

각 샘플을 양자화 한다.  
유한한 집합 T에 근접한 수로 각 샘플 값을 근사한다.  

결과 스트링은 이진수로 인코딩 된다.  


### 인코딩의 크기

샘플 수 * T 를 인코딩의 크기라고 한다.  

T 가 {A, B, C, D} 라고 할 때,  
위의 교향곡의 예는 130,000,000 * 2 bits = 37.5mb 이다.  

T 가 M 개의 기호로 이루어진다면, 이는 log2(M) 을 요한다.  

인코딩의 크기를 어떻게 줄일까?  

샘플 비율을 줄이거나, T 집합의 수를 줄인다.  



### 집합 T의 알파벳의 길이를 줄이기

그리디한 접근 방식을 이용하자.  

|Alphabet|Frequency|Conventional|Variable-length|
|:---:|:---:|:---:|:---:|
|A|70M|00|0|
|B|3M|01|001|
|C|20M|10|10|
|D|37M|11|11|

이로 인코딩되는 예는 다음과 같다.  

|Type|Formula|Size|
|:---:|:---:|:---:|
|Conventional|130,000,000 * 2 bits|260MB|
|Variable-length|70M * 1 bit + 57M * 2 bits + 3M * 3bits|193MB|

### 문제

하지만 지금 이 방법에는 문제가 있다.  
모호함 (ambiguity) 의 문제로,  
AAC 와 BA 는  
0 0 10, 001 0 => 둘 다 0010 이라는 코드로 인코딩 된다.  

따라서 이를 피하는 방법으로, Prefix-free encoding 방식을 적용한다.  

Prefix-free encoding 방법은  
어떤 코드도 다른 코드의 접두사가 되지 않고  
이진트리를 이용하여 나타낼 수 있다.  

따라서 허프만 인코딩은  
Variable length 와 Prefix-free 를 인코딩이다.  
이는 Optimal Merge Pattern Algorithm 과 비슷하다.  


### Prefix-free 한 코드 생성 

앞서 배운 Optimal Merge Pattern 의 알고리즘과 비슷하다.  
코드들을 빈도에 따라 오름차순으로 정렬한다.  

|Alphabet|Frequency|Conventional|Variable-length|Huffman code|
|:---:|:---:|:---:|:---:|:---:|
|A|70M|00|0|1|
|B|3M|01|001|000|
|C|20M|10|10|001|
|D|37M|11|11|01|


|Type|Formula|Size|
|:---:|:---:|:---:|
|Conventional|130,000,000 * 2 bits|260MB|
|Variable-length|70M * 1 bit + 57M * 2 bits + 3M * 3bits|193MB|
|Huffman code|70M * 1 bit + 37M * 2 bit + 23M * 3 bit|213MB|

