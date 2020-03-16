---
title : Language Models and RNNs 2
categories : [Natural Language Processing]
date : 2020-03-16
---

본 글은 스탠포드 대학의 '딥 러닝을 이용한 자연어 처리' (Natural Language Processing with Deep Learning),  
[CS224N의 6번째 강의](https://www.youtube.com/watch?v=iWea12EAu6U&list=PLoROMvodv4rOhcuXMZkNm7j3fVwBBY42z&index=6)를 번역, 요약 후 이해를 돕기 위한 부가설명을 추가한 글이다.  

# RNN (Recurrent Neural Network)

언어 모델, 그리고 그 중 fixed-window 신경 언어 모델은  
n-gram 언어 모델이 가지는 희소성과 공간에 대한 문제는 해결하였지만  

또 다른 문제로 학습 처리 과정의 비균등(No symmetry in how the inputs are processed)과,   
window를 늘릴 때의 가중치 행렬 W의 벡터 차원에 비례한 증가가 생겨났다.  

위 두 문제는 window가 고정되어 있기(fixed) 때문에 발생한다.  

그리고 이에 대한 대안으로, RNN(Recurrent Neural Network, 순환신경망)이 있다.  

![image](https://user-images.githubusercontent.com/22045424/76741041-32a90a00-67b2-11ea-9e9d-d34e7580ba0b.png)

위는 RNN의 가장 중요한 기능들을 간단하게 나타낸 다이어그램이다.  

RNN은 다른 언어 모델과 같이 입력 sequence 가 있다.  
다른 점은 hidden states에 있다.  
RNN에서는 다른 것들과 달리 hidden states가 input의 개수만큼 존재한다.  
(이전의 fixed-window 신경 언어 모델에서는 hidden state가 한 개였다.)  

이 hidden states는 하나의 상태 (single state)가 계속 변화하면서(mutate) 진행해나가는 것으로 생각해도 된다.  
하나의 상태가 여러가지 버전 (several version of same thing)이 생겨나는 것이다.  
때문에 이를 timestep이라고도 부른다. 이는 왼쪽 오른쪽으로도 이동할 수 있다. 
(양방향으로 가능한 RNN도 존재 : Bidirectional RNN)  

**여기서 중요한 점은, 가중치 행렬 W가 매번 똑같이 적용된다는 것이다.**  

즉 hidden states가 여러개라고 해서 공간을 그만큼 소모하지 않는다.  
또한 가중치 행렬은 하나면 되므로, 입력이 몇 개나 들어오든 상관이 없다.  

![image](https://user-images.githubusercontent.com/22045424/76741978-c4654700-67b3-11ea-9a0e-cf83dad36d05.png)

RNN이 학습하는 과정은 정확히 위의 사진과 같다.  

입력은 다른 모델들과 같이 one-hot 벡터로 받은 뒤  
look up matrix와 곱하여 단어에 맞는 벡터를 선택하고  
![We](https://latex.codecogs.com/gif.latex?W_%7Be%7D) 가중치 행렬과 ![et](https://latex.codecogs.com/gif.latex?e%5Et) 의 값(현재 상태), 그리고   
은닉층 가중치 행렬인 ![Wh](https://latex.codecogs.com/gif.latex?W_%7Bh%7D)과 이전 상태의 hidden states 벡터 ![ht-1](https://latex.codecogs.com/gif.latex?h%5E%7Bt-1%7D)의 곱을 더한 뒤  
편향 값을 더한 다음, 비선형 활성화 함수를 취하면 된다. 그림에서는 시그모이드 함수를 썼다.  

그리고 이 과정은 입력이 들어올 수록 계속된다.   
![h0](https://latex.codecogs.com/gif.latex?h%5E0)은 초기값(initial hidden state)으로,  
이전에 배웠던 값에서 온 변수여도 되고 0벡터로 해도 된다.  

오른쪽 끝에서 나온 현재 은닉층(current hidden state) ![h4](https://latex.codecogs.com/gif.latex?h%5E4)는  
선형 변환을 거친 후, 소프트맥스 함수로 단어별로 얼마나 이 문장과 어울리는지를 출력하게 된다.  


## 장점  

1. RNN은 어떤 길이의 입력도 처리할 수 있다.  
(n-gram이나 fixed-window 신경 언어 모델에서는 불가능했다.)  


2. (이론적으로) 단계 t에서 훨씬 이전의 단계에서 나온 정보를 이용하여 계산할 수 있다.  
일전에 n-gram 언어 모델에서 예를 들었던 감독관과 시간에 대한 문맥을  
n-gram이나 fixed-window 신경 언어 모델에서는 이용할 수 없었지만 RNN은 가능하다.  

3. 입력이 길다고 해서 모델의 크기가 커지지 않는다.  
![Wh](https://latex.codecogs.com/gif.latex?W_%7Bh%7D)나 ![We](https://latex.codecogs.com/gif.latex?W_%7Be%7D)은 입력이 길다고 해서 커지지 않는다. (임베딩 되는 벡터의 차원과 같음)  
fixed-window 신경 언어 모델에서는 입력이 커지면 (window의 증가) 가중치의 크기가 커졌다.  
또한 RNN에선 가중치를 계속해서 반복해 사용한다.  

같은 가중치가 모든 timestep에 적용되어 입력이 처리되는데에 균형이 있다.  
이전의 fixed-window 신경 언어 모델에서는 가중치가 나뉘어서 각각의 단어 벡터에 다르게 곱해지고,  
이 과정이 비효율적으로 반복됨을 알 수 있었다.  
하지만 지금은 저 위의 굵게 표시한 부분에서 알 수 있듯이 한 번 학습이 진행될 때 가중치가 일정하게 곱해지므로  
한 번의 학습이 모든 단어에 골고루 영향을 미치게 된다.  

## 단점  

RNN 연산은 느리다.  
RNN은 이전 단계의 값을 다음 연산에 사용하므로  
병렬 연산이 불가능하다.  
입력이 길면 길 수록 연산은 느려지게 된다.  

실제로는 먼 단계에 있는 정보를 이용하기가 어렵다.  
이론적으로는 가능하다고 장점에 적혀있지만 현실에서는 감독관이나 시간에 대한 정보를 활용하기 어렵다.  


## RNN으로 학습하기  

1. 단어를 ![x1xt](https://latex.codecogs.com/gif.latex?x%5E1%2C...%2Cx%5ET)로 나타낼 수 있는 큰 말뭉치를 준비한다.  

2. RNN 언어 모델에 넣고 모든 단계 t에 대하여 출력 확률 분포 ![yhatt](https://latex.codecogs.com/gif.latex?%5Chat%7By%7D%5Et)를 계산한다.  

3. 단계 t의 손실함수는 컴퓨터가 예측한 확률 분포 ![yhatt](https://latex.codecogs.com/gif.latex?%5Chat%7By%7D%5Et)와 ![yt](https://latex.codecogs.com/gif.latex?y%5Et) 사이의 교차-엔트로피이다.  

![cross-entropy](https://latex.codecogs.com/gif.latex?J%5E%7B%28t%29%7D%28%5Ctheta%29%20%3D%20CE%28y%5E%7B%28t%29%7D%2C%20%5Chat%7By%7D%5E%7B%28t%29%7D%29%20%3D%20-%5Csum%20_%7Bw%5Cin%20V%7D%5C%3B%20y_%7Bw%7D%5E%7B%28t%29%7D%5C%3B%20log%5C%3B%20%5Chat%7By%7D%20_%7Bw%7D%5E%7B%28t%29%7D%20%3D%20log%5C%3B%20%5Chat%7By%7D%20_%7Bx_%7Bt&plus;1%7D%7D%5E%7B%28t%29%7D)  

그리고 이 평균이 전체 트레이닝의 손실값(overall loss for entire training set)이 된다.  

하지만 말뭉치의 모든 단어에서 손실과 기울기를 계산하는 비용은 너무 비싸다.  
따라서 실제로는 ![x1xt](https://latex.codecogs.com/gif.latex?x%5E1%2C...%2Cx%5ET)를 문장이나 문서로 묶어 계산한다.  

이후 다른 신경망이 그렇듯 기울기를 계산해 가중치를 수정하고, 이 과정을 반복(배치로 나눠)하는 것이다.  



## 역전파 (backpropagation)

RNN에서는 ![Wh](https://latex.codecogs.com/gif.latex?W_%7Bh%7D)가 반복적으로 곱해진다.  
그렇다면 이에 관한 손실함수 ![lossfunction](https://latex.codecogs.com/gif.latex?J%5E%7B%28t%29%7D%28%5Ctheta%29)는 어떻게 구할까?  

![answer](https://latex.codecogs.com/gif.latex?%5Cfrac%20%7B%5Cpartial%20J%5E%7B%28t%29%7D%7D%7B%5Cpartial%20W_%7Bh%7D%7D%20%3D%20%5Csum%20_%7Bi%3D1%7D%20%5E%7Bt%7D%20%5Cleft.%5Cfrac%7B%5Cpartial%20J%5E%7B%28t%29%7D%7D%7B%5Cpartial%20W_%7Bh%7D%7D%5Cright%7C_%7B%28i%29%7D) 답은 모두 더하는 것이다.  

multivariable chain rule 에 의하면,  
![fxy](https://latex.codecogs.com/gif.latex?f%28x%2C%20y%29) 과 각각의 함수 ![xtyt](https://latex.codecogs.com/gif.latex?x%28t%29%2C%20y%28t%29)가 있다고 할 때  
t에 대한 함수 f의 기울기는 ![mult](https://latex.codecogs.com/gif.latex?%5Cfrac%7Bd%7D%7Bdt%7D%5C%3Bf%28%7B%5Ccolor%7BBlue%7Dx%7D%28t%29%2C%20%7B%5Ccolor%7BRed%7Dy%7D%28t%29%29%20%3D%20%5Cfrac%7B%5Cpartial%20f%7D%7B%5Cpartial%20%7B%5Ccolor%7BBlue%7D%20x%7D%7D%5Cfrac%7Bd%5Ccolor%7BBlue%7Dx%7D%7Bdt%7D%20&plus;%20%5Cfrac%7B%5Cpartial%20f%7D%7B%5Cpartial%20%5Ccolor%7BRed%7Dy%7D%5Cfrac%7Bd%5Ccolor%7BRed%7Dy%7D%7Bdt%7D)과 같이 구할 수가 있다.  

(편미분 때도 같은 식으로 진행하였다.)  

![image](https://user-images.githubusercontent.com/22045424/76748391-02676880-67be-11ea-8882-bb11515cecfb.png)   

따라서 이렇게 계속 더해나가면 역전파로 ![Wh](https://latex.codecogs.com/gif.latex?W_%7Bh%7D)에 대한 ![jtheta](https://latex.codecogs.com/gif.latex?J%5E%7B%28t%29%7D%28%5Ctheta%29) 를 구할 수 있다.  

역시 이렇게 학습한 RNN 모델로, 문장을 만들 수도 있다.  

![image](https://user-images.githubusercontent.com/22045424/76748729-9d604280-67be-11ea-8af3-eeae8743efb3.png)  

## 혼란도 (perplexity)  

언어 모델을 평가하는 지표로  
perplexity 라는 척도를 사용한다.  
perplexity는 네이버 사전에서 '당혹감'이라는 뜻으로 결과가 나온다.   
인공지능 분야에서 다른 단어 (신경망, 기계학습, 합성곱 등)와는 다르게  
이 perplexity 라는 단어는 사람들이 공통적으로 쓰는 한글 번역명이 없다.  
따라서 처음에만 '혼란도'라고 부르고 perplexity 라고 부르도록 한다.  

perplexity는 손실함수 (loss function)과 같이 이름에서부터 그 값의 의미를 유추할 수 있다.  
perplexity는 사람이 언어 모델이 예측한 결과를 봤을 때 얼마나 당혹감을 느끼는 지이다.  
이상한 말을 출력할 수록 perplexity는 증가하며  
말이 되는 말을 할 수록 perplexity는 감소한다.  
즉 perplexity는 적을 수록 좋다.  

perplexity는 다음과 같은 식으로 계산한다. LM은 Language Model의 약자이다.  

![perplexity](https://latex.codecogs.com/gif.latex?%5Ctextnormal%7Bperplexity%7D%20%3D%20%5Cprod%20%5ET%20_%7Bt%3D1%7D%20%5Cleft%28%20%5Cfrac%7B1%7D%7BP_%7B%5Ctextnormal%7BLM%7D%7D%28x%5E%7B%28t&plus;1%29%7D%7Cx%5E%7B%28t%29%7D%2C%20...%2C%20x%5E%7B%281%29%7D%29%7D%20%5Cright%29%20%5E%7B%5Cfrac%7B1%7D%7BT%7D%7D)  

위 식은 말뭉치의 확률을 전부 곱한 뒤 T의 제곱근을 씌워 단어의 수 대로 일반화를 해주는 것이다.  
일반화를 하지 않으면 값이 점점 커진다.  
또한 이 값은 계산하면 ![expjtheta](https://latex.codecogs.com/gif.latex?%5Ctextnormal%7Bexp%7D%20%28J%28%5Ctheta%29%29)가 된다.  
(equal to the exponential of the cross-entropy loss)  

## 활용

언어 모델은 다음과 같은 분야에서 활용된다.  

- 타이핑 예측  
- 음성 인식  
- 손글씨 인식  
- 철자/문법 정정  
- 저자 확인  
- 기계 번역  
- 요약  
- 대화  
- 등등...  

![image](https://user-images.githubusercontent.com/22045424/76750798-2fb61580-67c2-11ea-84e1-dd700931ae28.png)  

다음과 같이 마지막 hidden state의 값을 이용하여 문장을 임베딩할 수도 있다.  

마지막으로 지금 배운 RNN은 'vanilla RNN'이라고 불린다.  
RNN은 지금의 vanilla RNN 뿐 아니라 GRU, LSTM, 혹은 multi-stage RNN 등이 있다.  
