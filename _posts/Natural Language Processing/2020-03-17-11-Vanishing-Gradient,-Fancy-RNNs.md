---
title : Vanishing Gradients, Fancy RNNs
categories : [Natural Language Processing]
date : 2020-03-17
---

본 글은 스탠포드 대학의 '딥 러닝을 이용한 자연어 처리' (Natural Language Processing with Deep Learning),  
[CS224N의 7번째 강의](https://www.youtube.com/watch?v=QEw0qEa0E50&list=PLoROMvodv4rOhcuXMZkNm7j3fVwBBY42z&index=7)를 번역, 요약 후 이해를 돕기 위한 부가설명을 추가한 글이다.  

# 사라지는 기울기 (vanishing gradient)?

RNN에서의 역전파, 그 중 ![Wh](https://latex.codecogs.com/gif.latex?W_h)에 대한 ![jtheta](https://latex.codecogs.com/gif.latex?J%5E%7B%28t%29%7D%28%5Ctheta%29) 의 기울기는 ![JWh](https://latex.codecogs.com/gif.latex?%5Cfrac%20%7B%5Cpartial%20J%5E%7B%28t%29%7D%7D%7B%5Cpartial%20W_%7Bh%7D%7D%20%3D%20%5Csum%20_%7Bi%3D1%7D%20%5E%7Bt%7D%20%5Cleft.%5Cfrac%7B%5Cpartial%20J%5E%7B%28t%29%7D%7D%7B%5Cpartial%20W_%7Bh%7D%7D%5Cright%7C_%7B%28i%29%7D)가 된다고 하였다.  

이는 ![Wh](https://latex.codecogs.com/gif.latex?W_h)가 변하지 않기 때문에 이처럼 더하는 것으로 가능하다.  

하지만 hidden state 들의 값은 계속해서 변한다.  
이때 이 hidden state 에 대한 ![jtheta](https://latex.codecogs.com/gif.latex?J%5E%7B%28t%29%7D%28%5Ctheta%29) 의 기울기를 구하기 위해선  
chain rule 에 따라서 모든 hidden state 들의 변화를 곱해주어야 한다.  

결과는 ![chainrule](https://latex.codecogs.com/gif.latex?%5Cfrac%7B%5Cpartial%20J%5E%7B%28t%29%7D%7D%7B%5Cpartial%20h%5E%7B%281%29%7D%7D%20%3D%20%5Cfrac%7B%5Cpartial%20h%5E%7B%282%29%7D%7D%7B%5Cpartial%20h%5E%7B%281%29%7D%7D%20%5Ctimes%20%5Cfrac%7B%5Cpartial%20h%5E%7B%283%29%7D%7D%7B%5Cpartial%20h%5E%7B%282%29%7D%7D%20%5Ctimes%20%5Cfrac%7B%5Cpartial%20h%5E%7B%284%29%7D%7D%7B%5Cpartial%20h%5E%7B%283%29%7D%7D%20%5Ctimes%20%5Cfrac%7B%5Cpartial%20J%5E%7B%28t%29%7D%7D%7B%5Cpartial%20h%5E%7B%284%29%7D%7D) 과 같이 되는데,  
여기서 만일 곱해지는 기울기들이 작은 값을 가진다면,  
저 뒤로 가면 갈 수록 기울기가 작아질 것이다.  
즉 이 현상을 vanishing gradient, 사라지는 기울기라고 한다.  

vanishing gradient
![image](https://user-images.githubusercontent.com/22045424/76837870-9bf25100-6876-11ea-85bf-ef1e6a32724b.png)


## 증명 스케치

전 글에서 ![ht](https://latex.codecogs.com/gif.latex?h%5E%7B%28t%29%7D%20%3D%20%5Csigma%20%5Cleft%20%28W_hh%5E%7B%28t-1%29%7D%20&plus;%20W_xx%5E%7B%28t%29%7D%20&plus;%20b_1%20%5Cright%20%29) 라고 하였으므로  

이 h의 단계별 변화량은 ![hh](https://latex.codecogs.com/gif.latex?%5Cfrac%7B%5Cpartial%20h%5E%7B%28t%29%7D%7D%7B%5Cpartial%20h%5E%7B%28t-1%29%7D%7D%20%3D%20%5Ctextnormal%20%7Bdiag%7D%20%5Cleft%20%28%5Csigma%5E%7B%27%7D%20%5Cleft%20%28W_hh%5E%7B%28t-1%29%7D%20&plus;%20W_xx%5E%7B%28t%29%7D%20&plus;%20b_1%20%5Cright%20%29%20%5Cright%20%29%20%5Ctimes%20W_h) 가 된다.   

(h 앞에 ![Wh](https://latex.codecogs.com/gif.latex?W_h)가 곱해져 있으므로 chain rule 에 의해 뒤에 ![Wh](https://latex.codecogs.com/gif.latex?W_h)을 곱해준 것이다.)  

따라서 j단계의 h에 대한 i단계의 손실값의 기울기 ![jh](https://latex.codecogs.com/gif.latex?%5Cfrac%7B%5Cpartial%20J%5E%7B%28i%29%7D%28%5Ctheta%29%7D%7B%5Cpartial%20h%5E%7B%28j%29%7D%20%7D)는  

최종적으로 ![jhfinal](https://latex.codecogs.com/gif.latex?%5Cfrac%7B%5Cpartial%20J%5E%7B%28i%29%7D%28%5Ctheta%29%7D%7B%5Cpartial%20h%5E%7B%28j%29%7D%20%7D%20%3D%20%5Cfrac%7B%5Cpartial%20J%5E%7B%28i%29%7D%28%5Ctheta%29%7D%7B%5Cpartial%20h%5E%7B%28i%29%7D%7D%20W_h%20%5E%7B%28i-j%29%7D%20%5Cprod%20_%7Bj%20%3C%20t%20%5Cleq%20i%7D%20%5Ctextnormal%20%7Bdiag%7D%20%5Cleft%20%28%5Csigma%5E%7B%27%7D%20%5Cleft%20%28W_h%20h%5E%7B%28t-1%29%7D%20&plus;%20W_x%20x%5E%7B%28t%29%7D%20&plus;%20b_1%20%5Cright%20%29%20%5Cright%20%29) 이란 값을 가지게 된다.  
(diag은 인수를 대각행렬로 만드는 함수이다.)   
수식은 복잡하나, 대충 h의 기울기와 ![Wh](https://latex.codecogs.com/gif.latex?W_h)에 대한 ![jtheta](https://latex.codecogs.com/gif.latex?J%5E%7B%28t%29%7D%28%5Ctheta%29)의 곱을 떨어진 단계만큼 계속해서 곱해나가는 식으로 이해하면 된다.  
특히 ![Whij](https://latex.codecogs.com/gif.latex?W_h%20%5E%7B%28i-j%29%7D)이 작은 값을 가지게 되면 기울기는 사라지게 될 것이다.  
몬트리얼 대학교 AI학 박사 라즈반 파스카누의 2013년 논문  
<RNN 학습의 어려움에 대하여, (On The Difficulty of Training Recurrent Neural Networks)> 에선 이렇게 적혀있다.  

![image](https://user-images.githubusercontent.com/22045424/76842914-59347700-687e-11ea-8656-76515784c5be.png)  

"...Echo State Networks 은 반복되는 입력 가중치 (recurrent and input weights)를 학습하지 않아 기울기의 폭발, 사라짐 문제를 피했다.  
왜냐하면 보통 recurrent weight(![Wh](https://latex.codecogs.com/gif.latex?W_h)를 가리킴)의  
고유값(행렬을 선형변환이라 하고 벡터 a가 이 행렬로 변환된 벡터가 a의 상수배일 때 그 상수값) 중 가장 큰 값은 구조상 1보다 작아서  
**모델에 적용되는 정보가 기하급수적으로 빠르게 없어진다.**  
이는 이 모델이 길어지면 쉽게 다룰 수 없다는 것을 의미하며, 그 이유가 사라지는 기울기 문제와 약간 다르더라도 그렇게 된다."  

쉽게 말해 h의 기울기 값이 작지 않더라도 가중치의 행렬의 고윳값이  
(곱해지는 벡터를 선형변환 했을 때 변환되는 정도. 특정 벡터(고유벡터, 축)를 늘리거나 줄이는 양)  
대개 1보다 작은데, 이걸 계속해서 곱하게 된다면 (3단계 전에서 h가 미치는 J의 변화량을 구하려면 이 행렬을 세 번 곱해야 하므로)  
기울기는 계속해서 작아지게 되므로 (선형변환을 계속 거듭하는 것으로 이해해도 됨 -> 벡터의 크기가 작아짐)  
끝에 가선 기울기가 사라지게 된다.  
**기울기가 사라진다면 -> 학습이 진행되지 않는다.**  

여기서 비슷한 문제로 가중치 행렬의 고유값이 1보다 크다면 가중치가 폭발적으로 증가하는,  
이번엔 exploding gradients 문제가 존재한다.  

## 사라지는 기울기의 문제점

RNN 에서 출력은 단어 수만큼 가능하다.  
(이때의 출력은 다음 단어가 될 확률이 가장 높은 단어의 벡터, 또는 본 문장의 벡터)  
그 말인 즉슨 손실함수의 기울기 계산도 단어 수만큼 가능해서  
우리는 앞 단계에서 역전파된 기울기, 앞앞 단계에서 역전파된 기울기 등등을 계산할 수 있다.  

이렇게 RNN은 앞 단어, 앞앞 단어 등의 결과로부터 학습을 하여 자기 단계의 hidden state 벡터를 수정시킬 수 있는데  
이때 앞서 말한 사라지는 기울기 문제가 발생한다면  
먼 앞 단어에서는 학습을 하지 못하게 된다.  

멀리 떨어진 곳에서 오는 기울기 신호가 가까이서 오는 기울기 신호보다 훨씬 작기 때문에  
이 신호를 놓치게 되고, 따라서 모델의 가중치(여기선 h)는 먼 곳에서는 영향을 받지 못하고 가까이에서만 영향을 받는다.  
(Gradient signal from farway is lost because it's much smaller than gradient signal from close-by.  
So model weights are only updated only with respect to near effects, not long-term effects.)  
**(대충 모델이 글 전체의 문맥을 파악하지 못하게 된다는 내용)**

이것은 큰 문제가 될 수 있는 것이  
문맥을 파악하지 못할 뿐더러 문장의 연결성이 끊어지게 된다.  

# 의존성

여기서 Dependency에 대해서 알아보자.  
문장에는 의존성이라는 것이 존재한다.  

"Scientists study whales from space." 라는 이 문장은 사실 두 가지로 해석될 수가 있다.  
"과학자들이 우주에서 고래를 연구하다." 와 "과학자들이 우주에서 온 고래를 연구하다." 가 그것이다.  
전자는 우주에서 인공위성으로 고래의 개채 수를 파악한다던가 하는 새로운 연구 기법을 연상시키며 (실제 기사 : [BBC](https://www.bbc.com/news/science-environment-46046264)  
후자는 우주에서 나타난 외계 고래를 과학자들이 연구하는 SF 영화 혹은 소설에 관한 내용이 된다.  
이렇듯 단어는 어떻게 연결하느냐에 따라 문장의 뜻이 확 달라지고  
똑같은 언어를 배운 똑같은 지역, 나라의 사람이 읽어도 해석이 달라질 수가 있다.  
(한국어로는 "예쁜 친구의 동생을 만났다." 를 예로 들 수 있다. 친구가 예쁜 건지 친구의 동생이 예쁜 건지 헷갈리는 중의적 표현이다.)

이는 '의존성'이 잘못 매치가 된 사례 중 하나이다.    

![image](https://user-images.githubusercontent.com/22045424/76847497-7de01d00-6885-11ea-87b2-b5bb5147e413.png)  

위 사진에서 화살표는 head가 의존하는(dependent) 방향을 가리키고 있다.  
예를 들어 study는 연구를 하는 주체인 명사 Scientists에 의존하여 Scientists를 가리키고 있다.  
이때 'whales'가 'space'에 의존한다면 이는 우주에서 온 고래로 해석이 되는 것이고  
'study'가 'space'에 의존한다면 우주에서 연구하는 것으로 해석이 되는 것이다.  

이렇게 의존성에 따라서 문장의 구조를 나눈 것을 의존 문법 (dependency grammar)라고 하고  
문장을 이렇게 의존성을 분석하여 나누는 것을 의존 구문 분석 (dependency parsing) 이라고 한다.  

RNN 학습 과정에서 기울기가 사라진다는 것은  
= 단어와 단어 사이의 의존성이 없어진다  
= 구가 조금만 길어도 문장이 망가진다   
= semantic(의미의)한 측면을 잃어버린다는 뜻이다.   

또한 문법에서도 문제가 생기는데  
영문법의 경우, 일례로 동사의 단, 복수를 결정짓는 문제로  
The writer of the books ___ 를 들 수가 있는데  
이것의 답은 'is'나 'announces' 같은 단수 동사가 되지만  
기울기가 사라져 모델이 'writer'에서 영향을 거의 받지 않고 (Gradient signal from farway is lost)  
'books'에게서 크게 영향을 받게 되면 'are'나 'announce' 같은 복수 동사가 오게 된다.  
이는 역시 syntactic 을 잃는 것이다.  

## 폭발하는 기울기의 문제점

과유불급이라는 말이 있다.  
기울기는 너무 작아져도 너무 커져도 문제가 된다.  

신경망에서, 한 학습(epoch)이 끝나게 되면 가중치를 기울기와 학습률(learning rate)의 곱만큼 줄이게 된다.  
그런데 이 기울기가 커지게 되면 학습의 최종 목적지인 손실함수의 최소지점에 도달하기 어려워지며  
최악의 경우에는 이 곱이 무한대가 될 수도 있다.  
(이런 경우에는 학습을 다시 시작해야 한다.)  

이 문제를 해결하는 방법 중 Gradient clipping 이라는 방법이 있다.  
먼저 학습을 시작하기 전에 기울기 g에 대한 한계값을 지정해준 뒤  
우리가 계산한 기울기 g(물론 이때 이 g는 벡터이므로 일반화를 해주어야 함)가 이 한계값을 넘어섰을 경우  
g에 일반화된 g의 역수에 한계값을 곱한 값을 곱한다.  

![g](https://latex.codecogs.com/gif.latex?%5Chat%7B%5Ctextnormal%7Bg%7D%7D%20%3D%20%5Cfrac%7Bthreshold%7D%7B%5Cleft%20%5C%7C%20%5Chat%7B%5Ctextnormal%7Bg%7D%7D%20%5Cright%20%5C%7C%7D%20%5Chat%7B%5Ctextnormal%7Bg%7D%7D)  
(threshold : 한계값)  
(![norm](https://latex.codecogs.com/gif.latex?%5Cleft%20%5C%7C%20a%20%5Cright%20%5C%7C) : 벡터 a의 정규화  
![avector](https://latex.codecogs.com/gif.latex?a%20%3D%20%28x_1%2C%20x_2%2C%20...%2C%20x_i%29)일 때 ![normform](https://latex.codecogs.com/gif.latex?%5Cleft%20%5C%7C%20a%20%5Cright%20%5C%7C%20%3D%20%5Csqrt%20%7Bx_1%5E2%20&plus;%20x_2%5E2%20&plus;%20...%20x_i%5E2%7D)

![image](https://user-images.githubusercontent.com/22045424/76850213-42941d00-688a-11ea-95f0-d1bdeb7907f9.png)  

위 예시에서, 왼쪽의 완만한 경사에서 기울기가 내려오다가 그 경사 그대로 내려오게 되면  
앞에 절벽(cliff)가 있기 때문에  
왼쪽 사진처럼 저 접혀진 부분의 최소값을 지나쳐버리게 된다.(bad update) 
따라서 기울기를 줄여서 오른쪽 사진처럼 접혀진 사진으로 바로 내려갈수가 있다.  
오른쪽 사진에선  gradient clipping으로 단계의 사이즈를 줄여서 적게 내려가 최소부분을 지나치지 않고 도달할 수 있다.  

## 해결법  

사라지는 기울기 문제가 주로 문제가 되는 이유는   
timestep이 계속될 수록 정보를 보존하며 RNN이 학습하기가 어렵다는 점이다.  

그 이유는 vanilla RNN에선 hidden state가 계속해서  
![ht](https://latex.codecogs.com/gif.latex?h%5E%7B%28t%29%7D%20%3D%20%5Csigma%20%5Cleft%20%28W_hh%5E%7B%28t-1%29%7D%20&plus;%20W_xx%5E%7B%28t%29%7D%20&plus;%20b_1%20%5Cright%20%29) 의 식으로 반복하여 쓰여진다는 것인데  
이는 가중치의 곱의 선형 변환과 시그모이드 같은 비선형 변환이 반복되기 때문에 hidden state가 계속해서 변하게 된다.  

그렇다면 그 기억을 분리해 저장한 다음(separate memory)  
어떨 땐 적용시키고 어떨 땐 적용시키지 않게 된다면 어떨까? (변환 할지 안 할지 선택)  


# LSTM (Long Short-Term Memory)  

LSTM은 사라지는 기울기 문제를 막기 위해 고안된 RNN의 한 종류이다.  

LSTM은 단계 t에 hidden state ![ht](https://latex.codecogs.com/gif.latex?h%5E%7B%28t%29%7D)와 cell state ![ct](https://latex.codecogs.com/gif.latex?c%5E%7B%28t%29%7D)가 있다.  
위 두 벡터는 모두 n의 길이를 가진다.  
cell 은 장기에 걸친 정보(long-term information)를 저장한다.  
LSTM은 cell에서 정보를 지우고 읽고 저장할 수 있다. (마치 RNN 안의 컴퓨터 메모리처럼 작동한다.)  

그리고 이 동작은 gate에 의해 달라지는데  
gate는 역시 길이 n의 벡터이며, timestep마다 주어지는 gate의 각 원소는 열기(1), 닫기(0), 혹은 그 중간의 값을 가진다.  
만약 열게 된다면 정보는 지나갈 수 있고 닫으면 지나갈 수가 없다.   
gate는 동적이어서 값이 현재 문맥에 따라 바뀐다.  

gate에는 forget, input, output의 세 종류가 있다.  
그리고 단계별 gate 벡터를 계산하는 식은 다음과 같다.  

![gates](https://latex.codecogs.com/gif.latex?%5C%5Cf%5E%7B%28t%29%7D%20%3D%20%5Csigma%20%5Cleft%28W_f%20h%5E%7B%28t-1%29%7D%20&plus;%20U_f%20x%5E%7B%28t%29%7D%20&plus;%20b_f%20%5Cright%20%29%20%5C%5Ci%5E%7B%28t%29%7D%5C%3B%20%3D%20%5Csigma%20%5Cleft%28W_i%20h%5E%7B%28t-1%29%7D%20&plus;%20U_i%20x%5E%7B%28t%29%7D%20&plus;%20b_i%20%5Cright%20%29%20%5C%5Co%5E%7B%28t%29%7D%20%3D%20%5Csigma%20%5Cleft%28W_o%20h%5E%7B%28t-1%29%7D%20&plus;%20U_o%20x%5E%7B%28t%29%7D%20&plus;%20b_o%20%5Cright%20%29)  

(여기서 W는 ![Wh](https://latex.codecogs.com/gif.latex?W_h)에 해당하고 U는 ![Wx](https://latex.codecogs.com/gif.latex?W_x)에 해당한다.)  

forget gate는 이전 cell state에서 어떤 정보를 지우거나(forget) 가져갈지(keep)를 결정하고 (지우고)  
input gate는 위에서 계산된 새 cell의 내용에서 어떤 내용을 cell 에 쓸지를 정하고 (저장하고)   
output gate는 어떤 cell 의 내용이 hidden state로 출력될지를 정한다. (읽고)   
활성화 함수가 시그모이드인 점에서 알 수 있든 gate의 원소는 0~1사이의 값을 가진다.  

잘 보면 gate들은 이전 상태의 hidden state와 현재의 input인 x에 의해서 결정되는데  
따라서 gate들의 값은 동적으로 문맥에 따라 결정된다. (dynamic)  

새로이 계산된 cell state ![ctilde](https://latex.codecogs.com/gif.latex?%5C%5C%20%5C%7E%7Bc%7D%5E%7B%28t%29%7D)와  
그중 지울 건 지우고 (forget gate), 이전 cell에서 가져갈 건 가져간 (input gate) cell state, ![c](https://latex.codecogs.com/gif.latex?%5C%5C%20%7Bc%7D%5E%7B%28t%29%7D)  
cell state에서 출력된 값을 선별해 계산한 새로운 hidden state ![ht](https://latex.codecogs.com/gif.latex?h%5E%7B%28t%29%7D)는 다음과 같이 계산된다.   

![cellstates](https://latex.codecogs.com/gif.latex?%5C%5C%20%5C%7E%7Bc%7D%5E%7B%28t%29%7D%20%3D%20%5Ctanh%20%5Cleft%28W_c%20h%5E%7B%28t-1%29%7D%20&plus;%20U_c%20x%5E%7B%28t%29%7D%20&plus;%20b_c%20%5Cright%20%29%20%5C%5C%20c%5E%7B%28t%29%7D%20%3D%20f%5E%7B%28t%29%7D%20%5Ccdot%20c%5E%7B%28t-1%29%7D%20&plus;%20i%5E%7B%28t%29%7D%20%5Ccdot%20%5C%7E%7Bc%7D%5E%7B%28t%29%7D%20%5C%5C%20h%5E%7B%28t%29%7D%20%3D%20o%5E%7B%28t%29%7D%20%5Ccdot%20%5Ctanh%20c%5E%7B%28t%29%7D) 

첫째 줄에서는 이전의 Vanilla RNN과 마찬가지로 이전 단계의 hidden state와 현재 입력을 이용해 새로 계산을 하는 것이다.  
달라진 점이라면 활성화 함수로 시그모이드가 아닌 하이퍼볼릭 탄젠트 함수를 이용했다는 점이다.  

이렇게 계산된 새로운 cell state에서 과거 단계에서 잊을 것은 잊고 (![forget](https://latex.codecogs.com/gif.latex?f%5E%7B%28t%29%7D%20%5Ccdot%20c%5E%7B%28t-1%29%7D))   
가져갈 것은 가져간다. (![input](https://latex.codecogs.com/gif.latex?i%5E%7B%28t%29%7D%20%5Ccdot%20%5C%7E%7Bc%7D%5E%7B%28t%29%7D))   
  
이 cell state에서 내보낼 것을 내보내서 (![output](https://latex.codecogs.com/gif.latex?o%5E%7B%28t%29%7D%20%5Ccdot%20%5Ctanh%20c%5E%7B%28t%29%7D))  
최종적으로 해당 단계의 hidden state가 결정된다.  

이렇게 LSTM을 사용한다면, 많은 단계 동안 정보를 보존하는 것이 일반 RNN 보다 쉬워지며  
반복적으로 적용되는(recurrent) 가중치 행렬 ![Wh](https://latex.codecogs.com/gif.latex?W_h)가 hidden state 안의 정보를 쉽게 보존할 수 있다.  

LSTM을 쓴다고 사라지거나 폭발하는 가중치 문제가 생기지 않는다고 보장할 순 없지만  
먼 거리의 의존성 (예 : 수식구의 끝 단어가 수식을 받는 명사와 멀리 떨어진 경우)을 학습하는데 더 쉽다.  

여기서, cell 의 존재 (위에 서술한 방정식)도 역시 chain rule 에 의해 계속해서 작아지게 되는데  
이것이 생기지 않고 어떻게 사라지는/폭발하는 기울기 문제를 해결했다고 할 수 있는지 궁금증이 생길 수 있다.  

이에 대해 스탠포드 대학교 컴퓨터과학과 박사과정 학생 Abigail See는 말한다.  

"이전의 Vanilla RNN 에서의 hidden state는 앞 단계의 모든 정보(기울기)가 통과해야 하는 일종의 bottle neck (병목현상)을 일으킨다.  
그래서 결국에는 기울기가 작아지는 현상이 발생하지만  
LSTM에서는 cell이 있다는 것을 알아야 한다.  
LSTM에서 cell은 일종의 지름길 역할을 하는데,  
cell은 forget gate의 여부에 따라 이전의 정보를 가져올 수도 있고 그대로 남을 수도 있다.  
만일 cell이 그대로 남는다면 cell 덕분에 사라지는 기울기 문제는 생기지 않는다.  
이 말은 cell로 인해 과거의 cell과 미래의 cell이 연결될 수 있는 (시간 차의 과거, 미래가 아니라 이전 단계, 앞 단계를 말함)  
루트가 존재한다는 의미기 때문에 기울기가 사라지지 않는다."  

# GRU (Grated Recurrent Units)  

GRU 는 LSTM 보다 더 쉬운 대체 방안이다.  
각 단계마다 입력 ![xt](https://latex.codecogs.com/gif.latex?x%5E%7B%28t%29%7D)와 hidden state ![ht](https://latex.codecogs.com/gif.latex?h%5E%7B%28t%29%7D)가 있다. (cell state는 없음)  

LSTM 에서는 forget, input, output gate로 전 단계에서 잊을 것, 거기서 입력할 것, 출력할 것을 선택했다면  
GRU에서는 전 단계에서 reset할 것, 그리고 거기서 update 할 것을 선택한다.  

즉 이번에는 f, i, o가 아닌 u, r이 새로운 변수로 등장한다.  

역시 큰 틀은 같게  
![ur](https://latex.codecogs.com/gif.latex?%5C%5Cu%5E%7B%28t%29%7D%20%3D%20%5Csigma%20%5Cleft%28W_u%20h%5E%7B%28t-1%29%7D%20&plus;%20U_u%20x%5E%7B%28t%29%7D%20&plus;%20b_u%5Cright%20%29%20%5C%5Cr%5E%7B%28t%29%7D%5C%3B%20%3D%20%5Csigma%20%5Cleft%28W_r%20h%5E%7B%28t-1%29%7D%20&plus;%20U_r%20x%5E%7B%28t%29%7D%20&plus;%20b_r%20%5Cright%20%29) 의 식을 가진다.    

윗 줄의 update gate는 이전의 hidden state의 어떤 부분이 update 될 지, 아니면 보존될 지를 선택하고  
reset gate는 새 hidden state 을 계산하기 위해서 이전 hidden state의 어떤 부분이 사용될 지를 정한다.  

이 두 gate들을 이용하여 hidden state는 다음과 같이 게산되는데  
![gru](https://latex.codecogs.com/gif.latex?%5C%5C%20%5Ctilde%20h%5E%7B%28t%29%7D%20%3D%20%5Ctanh%20%5Cleft%28W_h%20%5Cleft%20%28r%5E%7B%28t%29%7D%20%5Ccdot%20h%5E%7B%28t-1%29%7D%20&plus;%20U_h%20x%5E%7B%28t%29%7D%20&plus;%20b_h%20%5Cright%20%29%20%5Cright%20%29%20%5C%5C%20h%5E%7B%28t%29%7D%20%3D%20%281%20-%20u%5E%7B%28t%29%7D%29%20%5Ccdot%20h%5E%7B%28t-1%29%7D%20&plus;%20u%5E%7B%28t%29%7D%20%5Ccdot%20%5Ctilde%20h%5E%7B%28t%29%7D)  

새 h는 이전의 h를 reset gate를 통과 시킨 다음 하이퍼볼릭탄젠트 함수를 취한 것이고  
실제 적용되는 h는 이전 h를 방금 계산된 새 h에서 update 시킬 만큼만 update 시킨 값이다.  

LSTM 과 비슷하게 전 단계에서 까먹을 건 까먹고, 그 다음 업데이트 할 것은 업데이트 하는 과정이다.  

위 식에서 update gate는 전 단계의 h와 새 h의 균형을 이루게 해주는 역할을 한다. (1-u)  

이는 LSTM과 달리 memory cell이 없어서 사라지는 기울기 문제가 생길 확률이 적다.  
GRU는 역시 장기적으로 기억을 유지할 수 있다.  
GRU는 물론 매개변수가 적기 때문에 LSTM 보다 빠르다.  
하지만 GRU가 항상 LSTM 보다 좋은 성능을 내는 것도 아니다.  
LSTM은 기본적으로 좋은 선택이다. 특히 데이터가 엄청 많거나 데이터의 문장이 긴 경우에 그렇다. (의존성이 길어지는)   
경험을 해봐야 아는 것으로, LSTM으로 시작을 해서, 만일 좀 더 효율성을 원한다면 GRU로 바꿔보는 것도 좋다.  

# 사라지는/폭발하는 기울기 문제가 그저 RNN의 문제인가?  

이는 모든 신경 구조에서 (feed-forward 나 합성곱 신경망) 발생할 수 있다. (특히 깊은 것들)  
chain rule (연쇄법칙)과 비선형 함수 때문에 기울기는 역전파가 되며 사라질 위험이 언제든 존재한다.  
때문에 밑의 층의 학습 속도가 저하되고 훈련하기 어렵다.  
이럴 때는 기울기가 직접적으로 연결되어 흐를 수 있는 경로를 만들어주는 것이 좋다.  
(새로운 feed-forward, 합성곱 신경망 구조가 이 방법을 이용)  

예 : Residual connections라고 알려진 ResNet  
x의 값을 ReLU를 거친 뒤 다시 x를 더해줌 (identity) -> 기본값으로 정보를 전달함 -> 기울기가 사라지지 않음  

layer들마다 다 연결해주는 DenseNet,  

dynamic gate에 의해 ResNet의 identity (x 그 자체)와 새로운 기울기의 통과가 통제되는 highway connections  

사라지는/폭발하는 기울기 문제는 모든 신경망에서 나타날 수 있지만  
RNN은 특히 반복되어 계산되는 과정이 많으므로  
RNN의 특성이라고 할 수 있다.  

# Bidirectional RNN 

Bidirectional 은 양방향이라는 뜻이다.  
지금까지의 RNN은 오른쪽으로만 진행이 되어,  
한 hidden state는 항상 이전 단계의 hidden state로만 계산이 되었다.  
왜냐하면, 일단 이 RNN도 언어 모델의 큰 가정인, 이전 단어에 영향을 받는다는 가정을 가져가기 때문이다.  
하지만 아쉽게도 두 가지 뜻을 가지는데, 문맥에 따라 뜻이 달라지는, 때론 아예 정반대의 뜻을 가지는 단어들도 존재한다.  
이를테면 '공중 부양'에서의 '공중'과 '공중 화장실'에서의 '공중'은 전혀 다른 뜻을 가진다.  

이를 동음이의어라고 하는데, 태어날 때부터 딥 러닝을 시작하여 지식을 쌓아오며  
일생 한 번 런타임 에러를 발생시킨 적이 없는 우리와는 달리 컴퓨터는 이를 구분하기를 어려워 한다.  

위 예의 '공중'은 앞 단어만 봐서는 알 수 없고 뒤의 '부양'이나 '화장실'을 봐야 그 뜻을 파악할 수 있다.  

이를 처리하기 위해 RNN을 오른쪽 뿐 아니라 왼쪽으로도 진행하는 양방향 RNN이 생겨났다.  

양방향 RNN은 다음과 같이 작용한다.  

![image](https://user-images.githubusercontent.com/22045424/76859995-a4aa4d80-689d-11ea-83d1-fb299d4d1c24.png)  

RNN의 진행 과정을 간단하게 ![RNN](https://latex.codecogs.com/gif.latex?%5Ctextnormal%7BRNN%7D%28a%2C%20b%29%20%3D%20%5Csigma%5Cleft%28W_h%20a%20&plus;%20W_x%20b%20&plus;%20b_1%20%5Cright%20%29) 라고 했을 때  
![image](https://user-images.githubusercontent.com/22045424/76860685-c6f09b00-689e-11ea-9b93-1aad3675d8ff.png)  

와 같은 계산을 할 수 있다.  
위 예는 영화 리뷰의 감성 분석을 하는 예로  
'the movie was terribly exciting!' 이라는 문장은 긍정의 표시이다.  

'terribly'라는 단어는 평소엔 '끔직하게'라는 뜻이지만  
이렇게 뒤에 'exciting'이라는 긍정의 단어가 올 경우 이를 강조하는 'extremely'의 정도로 볼 수 있는데  
기본적인 단방향 RNN에선 'terribly'의 앞의 'was'라는 단어만 보게 되므로  
'terribly'를 일반적인 입력값인 부정적인 의미가 내포된 벡터로만 계산하여  
전체적인 문장의 임베딩에 좋지 않은 영향을 줄 수가 있다.  

따라서 위의 구조를 보면 우리가 여태 썼던 오른쪽으로 가는 forward hidden state (빨간색)과  
왼쪽으로 가는 backward hidden state (초록색)을 둘 다 고려하여  
새로운 hidden state 를 만드는 것을 확인할 수 있다.  
이런 구조로 'terribly'의 뜻을 생각할 때  
앞의 'was'와 뒤의 'exciting'을 동시에 고려할 수가 있는 것이다.  

이를 비유하자면 (실제 박사 학생이 든 비유)  
길 건널 때 차가 왼쪽에서 오니까 왼쪽만 봐도 되지만  
차가 오른쪽 차선에서 와서 사고가 날 수도 있으니  
왼쪽 오른쪽을 둘 다 보는 것이라고 할 수 있다.  

양방향 RNN은 안타깝게도 언어 모델로는 활용할 수가 없다.  
왜냐하면 언어 모델로 쓸 때는 그 다음에 들어오는 입력값을 알 수가 없기 때문이다.  
대신 전체적으로 다 정해진 (entire input sequence) 입력을 넣을 때는 가능하다.  
이때 양방향 RNN은 강력한 성능을 보이며 이를 기본값으로 이용해야 한다.  

# Multi-layer RNN  

![image](https://user-images.githubusercontent.com/22045424/76861783-74b07980-68a0-11ea-829b-455b11962fc5.png)  

다층 RNN은 말그대로 층이 여러개인 RNN으로  
지금 배운 RNN은 hidden state가 하나이지만  
이 hidden state의 출력으로 또 다른 hidden state를 계산하고, 이를 위를 향해 나아가며 반복하는 구조이다.  
밑층의 RNN은 저수준의 특징을, 윗층의 RNN은 고수준의 특징을 계산하게 된다.  
이는 stacked RNN이라고도 불린다.  

순서가 1층 먼저, 그 다음 2층인지 아니면   
'the'의 세 개 층을 계산하고 다음에 'movie'로 넘어가는지에 대한 질문이 생길 수 있는데  
pytorch에서는 이 다층 RNN을 층 순서로 계산하지만  
순서는 상관 없다고 한다.  

이 다층 RNN은 역시 양방향과 같이 사용할 수도 있다.  

높은 성능을 내는 RNN은 주로 다층 RNN이다.  
2~4층 RNN이 좋은 성능을 낸다는 연구 결과가 2017년에 나왔으며 그 중 4층 RNN이 최고였다고 한다.  
8층 RNN 같이 높아지면 skip-connections/dense-connections같이  
위에서 설명한 graident를 연결해주는 다른 통로를 만들어주어야 한다.  

사실 층이 깊어지면 그만큼 계산할 때 드는 비용도 높아져서 너무 많이 쌓는 것도 좋지 않다.  
다층 RNN의 일종인 BERT (Bidirectional Encoder Representational from Transformers)은 24층인데   
이는 skipping-line 연결이 많아서 가능한 일이다.  

![image](https://user-images.githubusercontent.com/22045424/76862913-1e443a80-68a2-11ea-8428-efe25cec9504.png)  

BERT는 모든 레이어의 왼쪽 오른쪽으로 다 연결이 되어있음을 확인할 수 있다.  
BERT는 base와 large 모델로 나뉘는데 각각 12층, 24층이다.  
실제 논문을 확인해보면 24층의 BERT large가 base 보다 성능이 조금 더 좋은 것을 확인할 수 있다.  

출처 : [Pre-training of Deep Bidirectional Transformers for Language Understanding](https://arxiv.org/pdf/1810.04805.pdf), Jacob Delvin 외 3명, Google AI Language  
