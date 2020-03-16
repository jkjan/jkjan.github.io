---
categories : [Natural Language Processing]
title : Language Models And RNNs
date : 2020-03-16
---

본 글은 스탠포드 대학의 '딥 러닝을 이용한 자연어 처리' (Natural Language Processing with Deep Learning),  
[CS224N의 6번째 강의](https://www.youtube.com/watch?v=iWea12EAu6U&list=PLoROMvodv4rOhcuXMZkNm7j3fVwBBY42z&index=6)를 번역, 요약 후 이해를 돕기 위한 부가설명을 추가한 글이다.  

# 언어 모델과 RNN

언어 모델은 x1부터 xt까지의 단어가 주어졌을 때  
x(t+1)의 단어의 확률 분포를 나타낸다.  

즉 이는 x1부터 xt의 단어가 있을 때 x(t+1)의 조건부 확률   

![probablity](http://latex.codecogs.com/gif.latex?P%28x%5E%28%5Et%5E&plus;%5E1%5E%29%20%7C%20x%5Et%2C%20...%20%2C%20x%5E1%29) 로 나타낼 수 있다.  

이를 분류 작업으로도 볼 수가 있고 (classification task)  
언어 모델 (language model)이라고 부른다.  

핸드폰으로 문자를 할 때 키보드 위에 추천 단어가 뜨는 것이나  
구글 검색에서 다음 단어들이 자동 완성돼서 추천되는 것도 언어 모델의 일종이다.  

## n-gram

언어 모델의 일종으로 n-gram 언어 모델을 들 수 있다.  
n-gram 은 n개의 연속되는 단어의 모임이다. (A n-gram is a chunk of n consecutive words)  
