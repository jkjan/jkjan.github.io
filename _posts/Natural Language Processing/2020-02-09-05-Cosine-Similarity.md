---
title : Cosine Similarity
categories : [Natural Language Processing]
date : 2020-02-09
---

## 코사인 유사도

세 가지 단어 임베딩 방법에 대해서 알아보았다.  
각각의 임베딩 방법이 어디에 쓰이는지 알아본다.  

첫번째로 배웠던 단어 임베딩 방법은 단어의 수를 세는 Bag of words 가정, 그리고 그 가정을 이용한 TF-IDF 행렬이었다.  
그리고 그 행렬로 문서의 유사도를 측정할 수 있다고 하였다.  

행렬이 벡터로 표현될 수 있다는 점에 주목을 하자.  

V개의 단어, D개의 문서로 표현되는 VxD 행렬에서 각각의 열을 문서의 벡터라고 할 수 있다.  
그렇다면 두 문서가 비슷하다면, 그 벡터의 값 또한 어떠한 연관을 가질 것이다.  
이 연관을 알아내는 데에는 두 가지 방법이 있다.  

첫번째는 벡터 간의 거리를 이용하는 법이다.  

이 거리는 수학에서 유클리드 거리라고 한다.  
유클리드 기하학, 비유클리드 기하학이라는 단어를 아는가?  
우리가 대학 이전 배운 모든 기하학은 유클리드 기하학이다.  
타원면이나 곡면에서 진행한 것이 아닌 이상 말이다.  

즉 유클리드 거리라는 건 우리가 일반적으로 생각하는 그 두 점 사이의 거리가 맞다.  
벡터 사이의 거리를 계산하여 그 거리가 가까울 수록 (0에 가까울 수록) 유사하며 멀 수록 별로 상관이 없다는 뜻이다.  

두번째는 벡터 간의 각도를 이용하는 법이다.  

벡터 간의 각도가 작다면 비슷한 문장, 많이 벌어졌다면 연관이 적은 문장으로 보는 것이다.  
그렇다면 벡터 간 각도는 어떻게 구할 것인가?  

아래는 중학교 때 배웠던 각도에 따른 cos 의 크기이다.  

|구분|0|30|45|60|90|
|:---:|:---:|:---:|:---:|:---:|:---:|
|cos|1|2/sqrt(3)|1/sqrt(2)|1/sqrt(3)|0|  

```python
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.ticker import FuncFormatter, MultipleLocator

fig, ax = plt.subplots()
x = np.linspace(0, 4*np.pi,100)
y = np.cos(x)
ax.plot(x,y)
ax.xaxis.set_major_formatter(FuncFormatter(lambda val,pos: '{:.0g}$\pi$'.format(val/np.pi) if val != 0 else '0'))
ax.xaxis.set_major_locator(MultipleLocator(base=np.pi))
plt.show()
```

표도 필요 없이 당장 그래프만 보더라도 각이 벌어질 수록 코사인의 값이 작아지는 것을 확인할 수 있다.  
위의 코드는 matplotlib 으로 코사인(x) 의 그래프를 구하는 코드이다.  

![image](https://user-images.githubusercontent.com/22045424/74096894-45dc1080-4b48-11ea-9054-78dd865df549.png)

벡터 간의 코사인을 구하면 즉 벡터가 얼마나 벌어져있는지를 확인할 수 있다는 말이 된다.  
벡터 간 코사인을 구하는 공식은 아래와 같다.  

![image](https://wikimedia.org/api/rest_v1/media/math/render/svg/a9f8c962f73f83456742caa95c89970a18a97f2e)

두 벡터의 내적을 각 벡터의 원점으로부터의 거리의 곱으로 나눈 값이다.  

문서 유사도의 비교에서는 유클리드 거리를 이용한 방법보다는 코사인 유사도가 더 많이 쓰이는 것 같다.  
유클리드 거리를 이용한다면 두 벡터의 크기가 비슷하지 않다면 유사도가 적게 나올 수도 있다.  
그러나 각도를 이용한다면 두 벡터의 크기는 별로 중요하지 않다.  
문서, 문장의 유사도를 비교할 때는 두 문서, 문장이 동일한 경우가 그렇게 많지 않다. (검색어로 문서를 찾는다고 생각해보자)    
코사인 유사도를 사용하면 두 문서의 길이가 같지 않더라도 유사도를 측정할 수 있다.  

그럼 문장의 유사도를 확인하는 프로그램을 작성해보자.  

## 노래 추천

가수와 노래 제목을 입력하면 그 노래 가사와 비슷한 가사의 노래를 추천해주는 프로그램을 만들어보자.  

라이브러리는 판다, 넘파이, 사이킷런 등을 사용한다.  

데이터는 [kaggle](https://www.kaggle.com/mousehead/songlyrics) 이곳의 데이터이다.  

```python
import pandas as pd
import numpy as np
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import linear_kernel

data = pd.read_csv("songdata.csv", low_memory=False).head(20000)

cosine_sim = None
try:
    cosine_sim = np.load("song_processed.npy")
except IOError:
    data['text'] = data['text'].fillna('')
    tfidf = TfidfVectorizer(stop_words="english")
    tfidf_marix = tfidf.fit_transform(data['text'])

    cosine_sim = linear_kernel(tfidf_marix, tfidf_marix)
    np.save("song_processed",cosine_sim)

songIndices = pd.Series(data.index, index=data['song']).drop_duplicates()
artistIndices = pd.Series(data.index, index=data['artist']).drop_duplicates()

def recommendMeSimilarTo(artist, title, cosine_sim = cosine_sim):
    artistIndex = artistIndices[artist]
    songIndex = songIndices[title]

    index = None
    if songIndex.size > 1:
        for i in songIndex:
            index = artistIndex[artistIndex == i]
            if index.size > 0:
                break
        index = index.values[0]
    else:
        index = songIndex

    sim_scores = list(enumerate(cosine_sim[index]))
    sim_scores = sorted(sim_scores, key=lambda x : x[1], reverse=True)
    sim_scores = sim_scores[1:11]
    song_indices = [i[0] for i in sim_scores]
    result = data.iloc[song_indices]
    return result

myRecommendation = recommendMeSimilarTo("Aerosmith", "Dream On")

for i in range(0, len(myRecommendation)):
    print(myRecommendation['artist'].values[i], "-", myRecommendation['song'].values[i])
```

1 : 판다를 임포트한다.  
2 : 넘파이를 임포트한다.  
3 : 사이킷런의 Tfidf 행렬을 만드는 함수이다.  
4 : 행렬의 linear kernal (A^T x B) 을 구하는 함수이다.  
6 : 사이트에서 받은 csv 데이터를 판다 데이터로 만든다.  
9~: numpy 파일을 불러온다. 파일이 없어 IOError 가 난다면 다음 코드를 실행한다.  
12: csv 파일로 만든 data 는 'artist', 'song', 'link', 'text'의 열을 갖는다.  
    이때 이 'text'열은 노래의 가사인데, 이 중 null 값이 있다면 ''로 채워준다.  
    
13: tfidf는 english 라는 stop_words 사전을 쓰는 토크나이저이다.  
14: 해당 토크나이저를 이용해 data의 'text' 열의 벡터를 tfidf 행렬로 만든다.  
16: 코사인 유사도 행렬을 만든다. linear_kernel 은 앞 행렬을 뒤집고 뒤 행렬과 곱하는 것이다.  
    즉 행렬 곱(dot)연산을 하는 것인데, 이때 코사인과 같이 정규화된 값의 곱으로 나누지 않는 이유는  
    TfidfVectorizer가 이미 정규화된 벡터를 반환하기 때문이다. [사이킷런 공식문서](https://scikit-learn.org/stable/modules/metrics.html#cosine-similarity)

17: 계산 완료된 행렬을 파일로 저장한다.  
19: 노래에 대한 인덱스를 저장한다.   
20: 가수에 대한 인덱스를 저장한다.  
22: 가수와 제목을 받아 노래를 추천해주는 함수를 정의한다.  
23~: 인덱스를 불러온다.  
27~: 노래가 여러 곡일 경우 아티스트를 기준으로 또 한 번 찾는다.  
     같은 아티스트에 같은 노래일 경우 맨 앞 하나만 반환한다.    
36 : 데이터의 인덱스와 계산된 코사인 유사도의 쌍의 리스트이다.  
37 : 위 리스트를 코사인 유사도를 기준으로 내림차순으로 정렬한다.  
38 : 상위 10개만 추린다. (0번 인덱스는 무조건 자기 자신이다.)  
39 : 상위 10개에 해당하는 인덱스를 저장한다.  
40 : 마지막으로 데이터의 'song' 열에서 해당하는 인덱스로 찾은 노래들의 리스트를 반환한다.  
     iloc은 데이터를 정수의 순서 형태로 찾는다.  
     
     
실제 프로그램의 출력이다.  

![image](https://user-images.githubusercontent.com/22045424/74098906-d45c8c00-4b60-11ea-9e9e-a7e8e70458f1.png)
