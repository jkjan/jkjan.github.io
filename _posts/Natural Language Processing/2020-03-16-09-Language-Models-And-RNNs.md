---
categories : [Natural Language Processing]
title : Language Models And RNNs
date : 2020-03-16
---

본 글은 스탠포드 대학의 '딥 러닝을 이용한 자연어 처리' (Natural Language Processing with Deep Learning),  
[CS224N의 6번째 강의](https://www.youtube.com/watch?v=iWea12EAu6U&list=PLoROMvodv4rOhcuXMZkNm7j3fVwBBY42z&index=6)를 번역, 요약 후 이해를 돕기 위한 부가설명을 추가한 글이다.  

# 언어 모델

언어 모델은 ![x1](https://latex.codecogs.com/gif.latex?x%5E%7B1%7D)부터 ![xt](https://latex.codecogs.com/gif.latex?x%5E%7Bt%7D)까지의 단어가 주어졌을 때  
![x(t+1)](https://latex.codecogs.com/gif.latex?x%5E%7Bt&plus;1%7D)의 단어의 확률 분포를 나타낸다.  

즉 이는 ![x1](https://latex.codecogs.com/gif.latex?x%5E%7B1%7D)부터 ![xt](https://latex.codecogs.com/gif.latex?x%5E%7Bt%7D)의 단어가 있을 때 ![x(t+1)](https://latex.codecogs.com/gif.latex?x%5E%7Bt&plus;1%7D)가 그 다음 단어가 될 조건부 확률   

![probablity](http://latex.codecogs.com/gif.latex?P%28x%5E%28%5Et%5E&plus;%5E1%5E%29%20%7C%20x%5Et%2C%20...%20%2C%20x%5E1%29) 로 나타낼 수 있다.  

이를 분류 작업으로도 볼 수가 있고 (classification task)  
언어 모델 (language model)이라고 부른다.  

핸드폰으로 문자를 할 때 키보드 위에 추천 단어가 뜨는 것이나  
구글 검색에서 다음 단어들이 자동 완성돼서 추천되는 것도 언어 모델의 일종으로 볼 수도 있다.  

# n-gram

언어 모델의 일종으로 n-gram 언어 모델을 들 수 있다.  
n-gram 은 n개의 연속되는 단어의 모임이다. (A n-gram is a chunk of n consecutive words)  

앞 글에서 설명했듯 확률을 계산하려 할 때 맨 앞의 단어까지 다 고려하면  
확률은 매우 낮아지며 (1보다 작은 값을 계속 곱하므로)  
현실에서도 맨 앞의 단어는 뒤의 단어와 연관이 깊지 않으므로  
앞의 몇(n) 단어만 고려하면 된다.  

이때 n의 값이 몇이냐에 따라서  
unigrams(1), bigrams(2), trigrams(3), 4-grams(4)로 불리게 된다.  

확률 분포나 확률 모델이 한 샘플을 얼마나 잘 예측하는지를 측정하는 척도를 perplexity 라고 한다.  

스탠포드 연구로 월 스트리트 저널의 3800만 단어를 학습하고  
![perplexity](https://latex.codecogs.com/gif.latex?PP%28W%29%3D%5Csqrt%5B%5Cleftroot%7B-2%7D%5Cuproot%7B2%7DN%5D%20%7B%5Cprod%20_%7Bi%3D1%7D%20%5EN%20%5Cfrac%7B1%7D%7BP%28w_%7Bi%7D%7Cw_%7Bi-1%7D%29%29%7D%7D)
의 식으로 perplexity 를 계산했을 때  
n의 따른 결과는  
|unigrams|bigrams|trigrams|
|:---:|:---:|:---:|
|962|170|109|

와 같으며 이로써 n의 값이 높을 수록 정확해짐을 알 수 있다. (perplexity는 '당혹감'이란 뜻으로 값이 낮을 수록 좋다.)  
예컨데 I __ , I am __ , I am like __ 를 주었을 때  
첫번째보다는 두번째, 두번째보다는 세번째에서 더 좋은 예측을 할 것이다.  

모델이 존재하려면 어떤 가정을 따라야 한다. (assumption)  
n-gram 언어 모델은 ![x(t+1)](https://latex.codecogs.com/gif.latex?x%5E%7Bt&plus;1%7D) 가 이전의 n-1개 단어에만 의존한다는 가정에 따른다.  

이를 수식으로 나타낸다면  
![assumption](https://latex.codecogs.com/gif.latex?P%28x%5E%7Bt&plus;1%7D%7Cx%5E%7Bt%7D%2C%20...%20x%5E1%29%20%3D%20P%28x%5E%7Bt&plus;1%7D%7Cx%5Et%2C%20...%2C%20x%5E%7Bt-n&plus;2%7D%29)  

즉, 조건부 확률의 정의 ![cond_prob](https://latex.codecogs.com/gif.latex?P%28A%7CB%29%3D%5Cfrac%7BP%28A%5Cbigcap%20B%29%7D%7BP%28A%29%7D)에 따라  

![n-grams](https://latex.codecogs.com/gif.latex?%5Cfrac%7BP%28x%5E%7Bt&plus;1%7D%2C%20x%5Et%2C%20...%20x%5E%7Bt-n&plus;2%7D%29%7D%7BP%28x%5Et%2C%20...%20x%5E%7Bt-n&plus;2%7D%29%7D) 와 같다.  

위의 분자는 n-gram 의 확률, 분모는 (n-1)-gram 의 확률을 나타내는데,  
이 수식으로 언어 모델의 가정을 설명할 수가 있다.  
단어가 나아갈 수록 앞의 n-1개, 그 다음은 또 그 앞의 n-1개의 단어의 확률에 영향을 받는다는 것이다.  

단어, 단어 뭉치의 확률을 계산하는 방법은 단어를 세는 것이다.  

4-gram 언어 모델을 사용한다고 했을 때  
예문 '... as the proctor started the clock, the students opended their ___ ' 에서는  
(감독관이 시간 체크를 시작했을 때, 학생들이 ___ 를 열었다)

빈칸을 포함한 4개의 단어, 즉 빈칸 앞의 세 단어 'students opened their' 만 본다.  
앞의 'as the proctor started the clock' 부분은 버려지게 (discard) 된다.  

이때 빈칸을 w라고 했을 때  
'students opened their' 다음에 w라는 단어가 올 확률  
![w](https://latex.codecogs.com/gif.latex?P%28%5Ctextnormal%7B%5Cbf%7B%5Cemph%7Bw%7D%7D%7D%20%7C%5Ctextnormal%7Bstudents%20opened%20their%7D%29)은

![students](https://latex.codecogs.com/gif.latex?P%28%5Ctextnormal%7B%5Cbf%7B%5Cemph%7Bw%7D%7D%7D%7C%5Ctextnormal%7Bstudents%20opened%20their%7D%29%20%3D%20%5Cfrac%7B%5Ctextnormal%7Bcount%28students%20opened%20their%20%5Cbf%7B%5Cemph%7Bw%7D%7D%29%7D%7D%7B%5Ctextnormal%7Bcount%28students%20opened%20their%29%7D%7D) 로 쓸 수가 있다.  


## 예시

이 모델을 학습한 말뭉치(corpus)에서  
'students opened their' 라는 묶음이 1000번 나왔고  
'students opened their books' 라는 묶음이 400번 나왔다면  
P(books | students opened their) = 0.4 라는 결론이 나오게 된다.  

이때 'students opened their exams' 이 100번 나왔다면  
P(exams | students opened their) 는 0.1 의 값을 가지는데  
이때 언어 모델의 함정이 드러나게 된다.  

사실, 언어 모델은 순서도 중요하고  
확률도 다 실제 사람이 만든 데이터에서 기반하기 때문에 
언어 모델이 예측하는 값을 순서도 고려하므로 (P(opened their)와 P(students opened their)는 다르기 때문)
문법도 맞게 된다.  

하지만 이 예문의 경우  
'감독관이 시간 체크를 시작했을 때 학생들이 책을 펼쳤다.' 라는 문장보다는  
'감독관이 시간 체크를 시작했을 때 학생들이 시험을 시작했다.' 라는 문장이 더 어울린다.  

'책을 펼치다'라는 뜻의 'opened one's books' 라는 구가  
'시험을 시작하다'라는 뜻의 'opened one's exams' 라는 구보다 많이 쓰여서  
이런 결과가 나온 것이다.  

이는 전 글에서 쇼팽의 녹턴을 예로 든 것과 같은 상황이다.  

이 문제는 글의 문맥을 파악하지 못했기에 생기는 것이다.  
앞의 'clock'라는 단어로 이 '시간'이 중요하다는 상황, 문맥이 생겨났으므로  
'books'가 아닌 'exams'를 골라야 맞는 답이 나온다.  

하지만 앞의 단어를 충분히 고려하지 않았기 때문에 이것이 불가능하게 된다.  


## 희소성 문제 (sparsity problem)

n-gram 언어 모델은 또한 희소성 (sparsity)이라는 문제가 발생한다.  
어떠한 단어 묶음이 완전 말이 되더라도 내가 가르친 데이터에 없거나 극히 적으면  
확률은 0이 된다는 점을 기억하자.    

희소성 문제는 두 가지로 나뉜다.  

첫번째는 정답이 될 단어가 흔치 않은 상황에 한정하여 옳은 단어라서,  
데이터에 존재하지 않을 때,  

예를 들어 'students opened their petri dishes' 라는 구는  
주어진 상황이 '실험'이었다면 말이 된다.  
이는 흔치 않은 상황이고, 이것이 데이터에 없었다면  
이를 말이 안 되는 문장이라고 컴퓨터가 말할 것이다.  
하지만 이 문장은 흔치 않을진 몰라도 맞는 문장이다.  

이를 해결하기 위해 0인 값에 작은 값을 더해주게 된다. 이를 Smoothing 이라고 한다.  
다음 단어의 확률(every possible word that comes next)은 'least some small probablity' 최소한의 작은 확률을 가지게 된다.   

두번째는 정답을 계산할 때 앞의 구가 데이터에 존재하지 않을 때이다.  

조건부 확률을 계산할 때 계속 곱해나가므로, 중간에 이런 묶음이 있으면  
말이 되든 안 되든 결과는 0이 된다.  

데이터에서 'students opened their' 라는 묶음이 등장한 적이 없다면  
뒤에 뭐가 오든 무조건 결과는 0이 된다.  
이때 back-off 기법을 활용해 'opened their'라는 단어만 보고 확률을 계산하게 된다. (4-gram -> trigrams)  

예상했듯, n값이 높을 수록 희소성 문제는 더 심해진다.  
n값이 높을 수록 정확도는 올라가고 (낮은 perplexity), 대신 희소성 문제가 생기니  
적당한 n값을 찾아 적용해야 한다.  
이때 보통 5보다 큰 값을 쓰지 않는다고 한다.  

또한 n이 높을 수록 저장해야 하는 값들이 많아진다.   
말뭉치에서 보는 모든 n-grams 들의 수를 저장해야 하기 때문이다.  


## 실적용

실제 n-gram 모델을 적용하여 컴퓨터에게 문장을 만들도록 시킬 수 있다.  
데이터로 학습을 진행하고, 처음에 몇 단어만 쥐어주자. 

예를 들어 trigrams로 학습한 모델에 'today the'라는 단어를 준다면  
컴퓨터가 'today the' 다음에 올 단어 중 가장 확률이 높은 것을 고를 것이다.   
이 단어가 'price'라는 단어라고 하면, 'today the price'라는 문장이 만들어지고,  
이를 작업을 한 번 더 하면 'the price'라는 단어 뒤에 가장 어울리는 단어인 'of'가,  
이를 반복한다면 문장을 계속해서 만들어갈 수가 있다.  

이렇게 만들어진 문장은 굉장히 문법적이다. 물론 컴퓨터가 영문법을 알고 이렇게 쓸리는 만무하지만  
적어도 인간이 만들어낸 문장을 가르쳤기 때문에 완벽하게 문장을 문법적으로 쓸 수 있다.  
하지만 그 문장은 앞뒤가 맞지 않다. 이를 해결하기 위해 n값을 더 늘려야 하지만  
n을 늘리면 희소성 문제와 모델의 크기도 덩달아 증가한다.  


# fixed-window 신경 언어 모델 (Neural Language Model)  

언어 모델에 딥 러닝을 접목시켜 보자.  

단어를 한 벡터로 표현하기 위해 한 벡터를 신경망을 이용해 학습시켜  
단어를 벡터로 임베딩 하는 것이다.  

fixed-window 신경 언어 모델은 신경망과 비슷한 구조를 가진다.  

여기서는 n-gram 이라는 용어를 대신해 window 라는 단어를 사용한다.  
window 는 앞의 몇 단어를 고려할지를 나타내는 단어로, window가 4라면 예측하려는 부분의 앞 4개까지만 살핀다.  

![nlm](https://www.programmersought.com/images/720/ada6f4bb80b1904bc8430a89bb49f4d0.png)

맨 밑의 검은색 글씨들을 보자.  

i번째 단어를 택하려면, one-hot 벡터인 ![xi](https://latex.codecogs.com/gif.latex?x%5Ei)를 통하여 고르게 된다.  
단어들의 임베딩된 벡터들을 모아놓은 행렬 ![e](https://latex.codecogs.com/gif.latex?e)와 이 one-hot 벡터를 곱하면  
해당 단어의 임베딩된 벡터를 얻을 수 있다.  
단어 수를 V, 벡터의 차원을 D라 했을 때 행렬 ![e](https://latex.codecogs.com/gif.latex?e)는 DxV의 크기를 가지고,  
따라서 파란색 글씨에서, 이 행렬과 Vx1 크기의 행렬 ![xi](https://latex.codecogs.com/gif.latex?x%5Ei)와 곱하면 Dx1 크기의 행렬, 즉 D 차원의 벡터 - i번째 단어의 임베딩 벡터가 나온다.  

행렬 ![e](https://latex.codecogs.com/gif.latex?e)를 사전이라 생각하면 이 작업은  
여기서 i번째 벡터를 찾는 것(look up)으로 생각할 수 있는데  
따라서 이 행렬 ![e](https://latex.codecogs.com/gif.latex?e)를 look up matrix라고도 한다.  

이렇게 찾아진 ![e1](https://latex.codecogs.com/gif.latex?e%5E1), ![e2](https://latex.codecogs.com/gif.latex?e%5E2), ![e3](https://latex.codecogs.com/gif.latex?e%5E3), ![e4](https://latex.codecogs.com/gif.latex?e%5E4) 벡터들을 연결하여(concatenate) 최종적인 ![e](https://latex.codecogs.com/gif.latex?e) 행렬을 만든다.  

붉은 글씨인 hidden layer (은닉층)에서는  
여기서 구한 위에서 구한 임베딩된 벡터와 가중치를 곱하고 편향을 더하고 (affine)  
이를 비선형 함수 (활성 함수)에 넣어 h 값을 구한다.  

초록색 글씨, output 에서는 이 h를 출력 함수에 넣고 소프트맥스 함수를 취한 뒤  
![yhat](https://latex.codecogs.com/gif.latex?%5Chat%7By%7D)을 구하게 된다.  

이 벡터는 이제 V개의 단어마다 'the students opened their' 다음에 올 확률을 구하는 벡터가 된다.  
사람이 입력한 정답과 비교하여 손실함수와 역전파로 가중치를 수정한다.  

위 학습을 진행한다면,  
V개의 단어 중 'books'와 'laptops'라는 단어의 벡터가 가장 높게 나타날 것이다.  

## 장점

이 fixed-window 신경 언어 모델을 n-gram 언어 모델과 비교했을 때 장점은  
첫째로 희소성 문제가 사라진다는 점이다.  
n-gram 언어 모델은 데이터가 없을 시 모든 확률이 0이 돼버리는 희소성 문제가 있지만  
fixed-window 신경 언어 모델은 가중치와 임베딩 벡터로 구하므로 데이터에 없더라도 0이 되는 문제는 발생하지 않는다.  

둘째로, 단어를 계수한 모든 데이터를 저장할 필요가 없다.  

## 단점

첫째, window 의 개수가 너무 작다는 것이다.  
이렇게 되면 앞의 단어가 문맥 상 중요한데도 보지 못하는 문제가 발생한다.  

둘째, window 의 크기를 늘리게 되면 가중치 행렬 W도 증가한다.  
파란 글씨의 단계에서 만든 행렬 ![e](https://latex.codecogs.com/gif.latex?e)는  
Dx1 크기의 벡터 4개를 이은 (concatenated) 벡터기 때문에 4Dx1 크기의 벡터이다.  

가중치 행렬 W를 이와 곱하려면 Vx4D 의 크기를 가져야 한다. (그래야 마지막에 Vx1 행렬이 나옴)  
때문에 window 크기를 늘리면 늘어나는 저장공간의 수는 임베딩 되는 벡터의 차원만큼 증가한다.  
(the width of W grows as you increase the size of the window)  
하지만 window 의 크기는 절대 적당해질 수가 없다.  
희소성 문제가 사라진 시점에서, window의 크기는 크면 클 수록 높은 정확도를 보일 것이다.  

셋째, 입력값이 처리되는 과정이 균등하지 않다.  

![그림1](https://user-images.githubusercontent.com/22045424/76730405-83af0300-679e-11ea-80cb-4d433607b222.png)

가중치 행렬 W와 임베딩 벡터 ![e](https://latex.codecogs.com/gif.latex?e) 를 곱할 때의 과정을  
다음과 같이 그림으로 표현할 때  
1은 1끼리, 2는 2끼리 곱해지게 되는데,  
따라서 한 학습에서 배우는 것은 옆의 단어들과 공유하지 않는다.  
그저 한 번의 학습 과정을 (similar function)을 4번 반복하는 것이다.  

window 로 묶인 단어들에는 공통성(commonality)이 있음에도 불구하고  
이 과정을 4번이나 반복하는 것은 비효율적이라는 것이다.  

넷째로 가장 큰 문제로는, 이 fixed-window 신경 언어 모델은  
입력의 길이가 한정되어 있다는 것이다.  

위의 단점에서 생기는 문제들은 이 모델들을 window가 고정하여 단순화하는 가정으로 만들었다는 사실에서 오기 때문이다.  
(Most of these problems here come from the fact that we had to make this simplifying assumption that there was a fixed-window.)  

여기서 이제 새로운 신경망인  
RNN (Recurrent Neural Networks) 가 등장하게 된다.  
