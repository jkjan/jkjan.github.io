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


# 신경 언어 모델 (Neural Language Model)  

언어 모델에 딥 러닝을 접목시켜 보자.  

단어를 한 벡터로 표현하기 위해 한 벡터를 신경망을 이용해 학습시켜  
단어를 벡터로 임베딩 하는 것이다.  

신경 언어 모델은 신경망과 비슷한 구조를 가진다.  

여기서는 n-gram 이라는 용어를 대신해 window 라는 단어를 사용한다.  
window 는 앞의 몇 단어를 고려할지를 나타내는 단어로, window가 4라면 예측하려는 부분의 앞 4개까지만 살핀다.  

![nlm](data:image/png;base64,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)

