---
title : Language Model
categories : [Natural Language Processing]
date : 2020-02-08
---

## 2. 단어의 순서 고려

1번에서 단어의 빈도를 세는 것은 사실 언어를 학습하는 것과는 거리가 멀다.  
주제가 글의 단어 사용에서 나타난다고 가정하는 'Bag of words' 는  
주제를 나타내는데는 쉬울지는 몰라도 우리가 말하는 실제 언어를 표현하기에는 어려움이 있다.  



만일 19세기 후반 인상주의 음악에 대한 이야기를 하는 글에서 다음과 같은 문장이 나왔다고 하자.  

"드뷔시는 프랑스의 유명한 ??? 이다."  
1. 피아니스트, 2. 독일, 3. 표현하다  

다음에 들어갈 단어는 누가 오든 드뷔시가 뭔지는 몰라도 1번 피아니스트라고 고를 것이다.  
이 답은 이 글을 BoW로 임베딩했다면 죽었다 깨어나도 맞추지 못할 것이다.  
이 문서는 19세기 후반 인상주의 음악에 관한 문서로,  
인상주의와 대비되는 독일이나, 무언갈 표현한다는 단어가 많이 등장할 것이고 피아니스트라는 단어는 그보다는 적을 것이다.  
아마 이 문서를 TF-IDF 행렬로 임베딩해서 맞추려고 했다면 3번을 꼽을 것이다.  

그럼에도 우리가 피아니스트라고 맞출 수 있는 이유는 첫째로, 단어의 순서를 보아하니 뭔가 물건이나 사람 이름이 나올 것 같고  
둘째로 프랑스의 유명한 독일이라는 표현은 말이 안 되기 때문이다.  
이렇듯 우리는 어떤 단어를 맞출 때 문맥을 고려하고, 문맥은 그 단어 주변의 단어들로 나타내진다.  

## 다음에 어떤 단어가 올 확률

한국어는 교착어라 단어를 나누는데 의미적으로 어려움이 있으므로 여기선 영어로 설명한다.  

"쇼팽은 저녁이나 밤에 대한 예술작품을 뜻하는 녹턴이라 불리는 총 21개의 피아노 곡을 작곡했다."  
"Chopin composed total 21 piano pieces which are called 'Nocturne', meaning a work of art dealing with evening or night."  

쇼팽에 대한 문서를 모아서 각 단어, 혹은 구 뒤에 어떤 단어가 올 확률을 계산한다고 하자.  
'chopin', 혹은 'Chopin' 이란 단어가 나오는 수를 구한다.  
그리고 'chopin composed' 가 나오는 수를 구한다.  
'Chopin composed piano...', 'Chopin composed waltzes...' 와 같은 부분이 해당한다.  
그리고 뒤의 수를 앞의 수로 나누면 Chopin이 등장했을 때 Chopin 다음에 바로 composed 란 단어가 올 확률이 된다.  
P(Composed | Chopin) = P(Chopin ^ Composed) / P(Chopin)   이라는 조건부 확률에 해당한다.  
어차피 나누면 전체 문장 수는 사라지니까 수만 필요한 것이다.  
양변에 P(chopin) 을 곱하면 P(Chopin ^ Composed) = P(Chopin)P(Composed | Chopin) 이 된다.   

그렇다면 이를 계속해서 적용시킬 수 있다.  
chopin, composed, 다음에 total 이 나올 확률 P(chopin ^ composed ^ total) 는  
P(chopin)P(composed | chopin)P(total | chopin composed) 일 것이다.  

이를 일반화하면, 어떤 문장의 단어를 x1, x2, x3... 이라 할 때  
그 문장의 확률 P(x1 ^ x2 ^ x3 ^ ...) = P(x1)P(x2|x1)P(x3|x1^x2)... 이다.  

하지만 확률은 1보다 작거나 같기 때문에,  
또한 이 문서가 똑같은 문장을 계속 복사 붙여넣기 한 문서가 아니라면 1보다 작기 때문에  
문장이 나올 확률은 굉장히 작아진다.  
따라서 이런 방식으로 문장을 유추하기는 굉장히 어렵다.  

"Chopin composed total 21 piano pieces which are called 'Nocturne', meaning a work of art dealing with evening or night."  
"Chopin composed total 21 piano pieces which are called 'Nocturne', meaning a work of architecture dealing with evening or night."  

쇼팽에 대한 문서로 확률을 구한 다음 이 두 문장을 던져주고 각각이 맞는 문장일 확률?  
문장이 길어서 둘 다 0에 수렴할 것이라 맞추지 못할 가능성도 있다.  
왜냐면 문장이 길어질 수록 그 확률은 0에 가까워질 것이고 제대로 카운트되지 않기 때문이다.  
이 문장이 맞는 문장인지 검증하는 프로그램에 쓴다고 하자.  
아무리 완벽한 문장이라도 데이터에서 단 한 번도 같은 순서로 등장한 적 없는 문장이라면  
무조건 틀렸다는 결과만 나올 것이다.  

그래서 문장 전체를 확률로 보기 보다는 해당 단어의 주변 몇 개만 보는 n-gram 이라는 모델을 사용한다.  

n-gram 에서 n 은 주변 몇 개까지 고려할지를 말한다.  
위와 같은 문장에서 "a work of art dealing with evening or night"에서 "night" 라는 단어가 나올 확률을  
"Chopin composed..." 부터 시작해서 다 보지 말고 n개씩만 보자는 것이다.  
n = 4 가 되면 night 를 포함해 단어 4개만 본다는 뜻이다.  
따라서 4-gram 일 때 "with evening or night" 순서로 나타날 확률은  
P(with evening or night) = P(with)P(evening | with)P(or | with evening)P(night | with evening or)이다.  

단, 이 데이터에 "with evening or" 다음에 "morning" 이라는 단어가 "night" 라는 단어보다 많이 쓰였다고 가정하자.  
이렇게 된다면 컴퓨터는 "Chopin composed ... Nocturne... with evening or morning." 이라는 문장을 더 그럴듯한 문장이라고 평가할 것이다.  

문법적으로 아예 말이 안 되는 것은 아니고, 그 네 단어만 잘라놓고 보면 이상한 것은 아니지만  
전체 문장을 보면 일단 Nocturne 이라는 단어도 나오고, 저녁이나 아침밥을 말하는 것도 아니므로  
사람들한테 보여주면 보통 night 라는 단어를 고를 것이고 그게 맞다.  

이렇듯 n-gram 에서 n 은 결과나 성능에 지대한 영향을 끼친다.  
실제 사용할 때 n 은 5를 넘어서면 안 된다고 한다.  

n-gram에서 n을 적당히 조절하더라도 문제점은 발생한다.  
만일 데이터가 학습하지 않은 순서가 들어온다면 컴퓨터는 그 확률을 0으로 계산할 수 밖에 없다.  
극단적인 예로 컴퓨터가 과장하는 부사나 형용사가 없는 문장만 학습했다면  
"I really like Chopin." 같은 문장을 아주 엉터리 문장으로 인식할 것이다.  
P(really | I) 가 0이기 때문이다.  
따라서 이 점을 보완하기 위해 백오프나 스무딩이라는 방식을 사용한다.  

백오프는 만일 해당 문장의 확률이 0일 경우(빈도가 0), n을 줄인 다음 상수 알파를 곱하고 상수 베타를 더해서 계산한다.  
이때 알파와 베타는 실제 빈도와의 차이를 보정하는 파라미터이다.  

스무딩은 모든 빈도에 k만큼 더하는 기법이다. 때문에 Add-k 스무딩이라고도 불리운다.  
이때 k=1일 경우 라플라스 스무딩이라고 한다.  
이를 통하여 높은 빈도를 가진 문자열의 등장 확률을 깎고, 전혀 등장하지 않는 것들의 확률을 조금이나마 높일 수 있다.  

n-gram 모델은 bag of words 보다는 문장의 적합도를 판별하는데 좋을 순 있으나,  
언어는 순서가 전부가 아니듯이 완벽한 모델이라고 하기는 어렵다.  
쇼팽, 피아노, 연주, 같이 연속적으로 등장하는 단어에서는 높은 적중률을 보일 순 있으나  
단어 간의 관계, 분포를 정확히 파악하는 데에는 어려움이 있고  
새로운 단어나 표현에는 약한 모습을 보일 수가 있다.  

그래서 단어를 다른 측면으로 접근하는 방법이 필요하다.  

바로 어떤 단어들과 같이 쓰이는지, 주로 어떤 위치에 나타나는지를 알아내는  
분포 가정 (distributional hypothesis) 라는 것을 이용해 단어를 임베딩하는 방식이다.  
