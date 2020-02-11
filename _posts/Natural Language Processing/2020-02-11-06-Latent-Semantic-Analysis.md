---
title : Latent Semantic Analysis
categories : [Natural Language Processing]
date : 2020-02-11
---

## Semantic and Syntactic

앞으로 자연어 처리 분야에서 Semantic 과 Syntactic 이라는 단어를 자주 접하게 된다.  
Semantic 이라는 단어는 의미적, Syntactic 이라는 말은 구문, 문법적이라는 뜻이다.  
Semantic 과 Syntactic 은 쓰이는 분야가 다르다.  
예컨데 문장 유사도, 혹은 문서의 주제를 고르는 일에서는  
아무래도 Syntactic 을 덜 고려하게 된다.  

기계 번역 같은 일에서는 이 둘을 동시에 고려하게 될 것이다.  
의미만 있고 문법적으로 옳지 않다거나,  
문법만 맞고 도통 말이 되지 않으면 틀린 결과이다.  

이번에 알아볼 것은 문서에서 주제를 뽑아내는 방법인 잠재 의미 분석(Latent Semantic Analysis, LSA)이다.  


## 복습

이전에 TF-IDF 를 알아보았다.  
TF 는 문서별 단어가 몇 개 나오는지를 센  
문서의 수 x 단어의 수 만큼의 행렬 Document Term Matrix 이다.  

여기에 단어가 몇 개의 문서에서 등장하는지 센 DF (Document Frequency),  
그리고 이 DF의 역수에 로그를 취한 값인 IDF (Inverse Document Frequency) 를 곱하면  
문서 별로 단어 개수 차원에 해당하는 벡터가 만들어진다.  
그리고 이 문서의 수 x 단어의 수 행렬을 TF-IDF (Term Frequency - Inverse Document Frequency) 라고 한다.  

여기서 문서별 벡터들끼리의 코사인을 계산하여 이 코사인 유사도로 문서 간 유사도를 측정할 수 있고  
이 유사도를 통해 어떤 문서가 주어졌을 때 가 문서와 가장 유사한 문서를 꼽을 수 있다고 하였다.  

TF-IDF 행렬은 문서를 벡터화하여 간편하게 문서 간 유사도를 측정할 수 있는 장점이 있지만  
단어의 수만 셀 뿐 단어들의 의미를 고려하지 못한다는 단점이 있었다.  
즉 'master'라는 의미의 단어가 여러번 나오면  
그 단어가 무슨 뜻인지는 전혀 고려하지 않은 채  
다른 'master'가 많이 등장한 문서와 유사하다는 결과를 출력하는 것이다.  

또한 맹목적으로 단어의 수만 세기 때문에  
다른 비슷한 문서들끼리의 범주를 만들기가 어렵다.  

이에 대한 대안으로 개별적인 문서들의 주제가 아닌,  
다른 문서에 자주 등장하는 단어까지도 고려해서 그 문서의 잠재된 의미를 끌어내는  
잠재 의미 분석 (Latent Semantic Analysis) 이라는 기법이 있다.  



## LSA

LSA 는 기본적으로 TF-IDF 행렬을 이용한다.  
LSA 는 TF-IDF 를 특이값 분해한 행렬들을 이용하는 것이기 때문이다.  
특이값 분해, Single Value Decomposition, 줄여서 SVD는  
m x n의 행렬 A 를  
m x m의 직교행렬 U,  
m x n의 대각행렬 Σ,  
n x n의 직교행렬 V의 전치(Transpose)의 곱, 즉 A = UΣVT 로 나타내는 것이다.  

여기서 직교행렬 (orthogonal matrix) 는  
A x AT = I 가 되는 행렬 A 를 뜻하고 (즉 AT = A^-1)  
대각행렬 (diagonal matrix) 는  
행렬의 i행, j열의 값이 i=j 일 때 0이 아닌 실수를, 이외에는 모두 0이 되는 행렬이다.  
이때 그 값은 i가 커질 수록 작아진다.  

물론 모든 행렬에 대해서 실수든 복소수든 이 SVD가 되는 세 행렬이 존재함이 [증명](https://en.wikipedia.org/wiki/Singular_value_decomposition#Existence_proofs)되었다.  

LSA 는 특별히 이 SVD에 대해서 잘려진 (truncated) 행렬들을 사용한다.  
잘려졌다는 것은, 예를 들어 내가 문서들의 주제를 5가지로 나누었다고 치면  
U 행렬에서 왼쪽 5열만, Σ 행렬의 1, 1에서부터 5, 5까지만, VT 행렬에서 위 5개 행만 사용하는 것이다.  
그렇게 되면, 문서의 개수를 d, 단어를 v, 라고 할 때  
d x v 행렬 TF-IDF는 v x 5, 5 x 5, 5 x v 인 U, Σ, VT 행렬의 곱으로 나타낼 수 있고,   
이때 여기 5에 해당하는 것을 t, 토픽의 개수라고 한다.  

LSA 는 이 행렬 U, Σ, V에 문서의 주제별로 숨겨진 의미가 있다고 본다.  

## LSA가 잠재 의미를 가지는 방법

설명은 스탠포드 대학교의 유튜브 채널을 참조한다. (출처 : [YouTube](https://www.youtube.com/watch?v=P5mlg91as1c))

![image](https://user-images.githubusercontent.com/22045424/74239925-36adbc00-4d1c-11ea-82d5-3c765ce7832d.png)

다음과 같이 분석된 TF-IDF 행렬이 있다고 가정한다.  
좌측부터 어벤져스, 스파이더맨, 배트맨의 히어로 영화 세 편, 그리고 클릭, 기생충의 가족 영화 두 편이다.  

그리고 이 행렬에 대해서 두 가지 토픽으로 나눈다고 한다. (t = 2)  
이 행렬을 7 x 2, 2 x 2, 2 x 5 의 Truncated SVD 된 행렬들은 다음과 같다.  

U :  
![image](https://user-images.githubusercontent.com/22045424/74240557-98baf100-4d1d-11ea-9b65-d172fd40d604.png)

이 행렬은 앞에서 언급했던 것과 같이 7 x 2 행렬이다.  
굵은 숫자들을 보면 다른 값들 보다 유독 절대값이 큰 값들이다.  
이 U 행렬은 행은 단어, 열은 주제이다. 개념(주제) 1, 2에 대한 단어들의 값이라고 할 수 있다.  
math, crime, fight, hero 같은 단어들은 히어로 영화에서 큰 값을 가지고,  
love, feeling, family 같은 단어들은 가족 영화에서 큰 값을 가지는 것을 확인할 수 있다.  

다음 Σ 행렬은 이렇다 :   
![image](https://user-images.githubusercontent.com/22045424/74240600-aec8b180-4d1d-11ea-855d-5f4cd8b36e87.png)

이 행렬은 2 x 2 행렬이다.  
이 행렬의 0이 아닌 값들은 각 주제들의 세기를 나타낸다.  
히어로 영화가 세 편, 가족 영화가 두 편으로, 히어로 영화에서 세기가 큼을 알 수 있다.  

VT :   
![image](https://user-images.githubusercontent.com/22045424/74240619-b5efbf80-4d1d-11ea-9593-ad93c659bc20.png)

이 행렬은 행은 주제를, 열은 영화를 나타낸다.  
즉 히어로 영화에서 어벤져스, 스파이더맨, 배트맨은 큰 값을 가지게 되고  
반면 가족 영화에선 클릭, 기생충이 큰 값을 가진다.  

정리하자면 U 는 단어 - 주제,  
Σ 는 주제 - 주제,  
VT 는 주제 - 문서의 관계를 나타내는 행렬이라고 할 수 있다.  

이렇게 TF-IDF 행렬을 U, Σ, VT 행렬로 Truncated SVD 를 하면  
VT 행렬에서 각 문서가 어느 주제에 해당하는지 볼 수 있고  
그 문서에 해당하는 단어들에는 어떤 단어가 있는지 U 행렬에서 확인할 수 있다.  

## 적용

CNN 의 뉴스 기사는 크게 8가지의 카테고리로 나뉜다.  
World, US Politics, Business, Health, Entertainment, Style, Travel, Sports 가 그것이다.  
CNN 의 기사 파일을 받아서, 이를 카테고리별로 나누고 그에 맞는 상위 5개 키워드를 알아보고자 한다.  
파일은 [여기](https://drive.google.com/uc?export=download&id=0BwmD_VLjROrfTHk4NFg2SndKcjQ)에서 받을 수 있다.  

위 파일은 9만개가 넘는 .story 파일이 압축되어 있다.   
메모장으로 열어보면 CNN 기사가 나오는 그냥 txt로 저장해도 되는 파일인데 .story로 저장이 되어있다.  
나는 C++의 fstream으로 디렉토리 내의 모든 .story 문서를  
한 txt 파일로 합친 후 데이터 학습(?) 을 진행하였다.  

코드는 아래와 같다.  

```C++
#include <iostream>
#include <dirent.h>
#include <cstring>
#include <fstream>

int main() {
    DIR *dir;
    struct dirent *ent;

    std::ofstream all;
    all.open("all.txt");

    if ((dir = opendir ("경로 ~ cnn/stories")) != NULL) {
        /* print all the files and directories within directory */
        while ((ent = readdir (dir)) != NULL) {
            char word[1024];
            if (ent->d_name[0] != '.') {
                char dirname[1024] = "경로 ~ cnn/stories/";
                std::ifstream file;
                file.open(strcat(dirname, ent->d_name));
                while (!file.eof()) {
                    file >> word;
                    all << word << " " ;
                }
                file.close();
                all << "\n";
            }
        }
        closedir (dir);
        all.close();
    } else {
        perror("no file");
        return EXIT_FAILURE;
    }

    return 0;
}
```

위 코드를 실행하면 해당 cpp 파일이 있는 곳에 all.txt 라는 파일이 생성이 된다.  
파일은 371mb 정도가 나올 것이다.  

먼저 all.txt 파일이 생성이 됐으면  
이를 읽어서 토크나이즈를 한다.  
문제는 토크나이즈를 하면 단어가 다 쪼개지는데  
사이킷런의 Tfidf 는 문서 단위로 입력을 받는다.  
그래서 토크나이즈를 하고 다시 다 합쳐줘야 한다.  

먼저 토크나이즈를 하는 부분이다.  

```python
cnnFile = open("../data/English/raw/cnn/all.txt", "r", encoding='UTF8')
    cnnDoc = []
    cnnTokenized = []
    while True:
        line = cnnFile.readline()
        if len(line) == 0:
            print("File read finished")
            break
        line = re.sub("[^a-zA-Z ]", "", line)
        tokenized = word_tokenize(line)
        for word in tokenized:
            if word == "CNN" or word == "highlight":
                tokenized.remove(word)
        cnnTokenized.append(tokenized)
```

정규식으로 문자를 먼저 걸러준 다음 nltk의 토크나이즈를 한 번 거친다.  
그리고 cnn 기사 파일에는 CNN이랑 @highlight 같은 단어가 많이 나와서 이들을 삭제해준다.   
(nltk의 사용자 사전을 찾아보려고 했는데 간편하지가 않아서 그냥 이렇게 하는게 나을 거 같다.)  

그리고 tfidf 를 위해 다시 합친 문장도 저장한다.  

```python
sent = ""
for w in tokenized:
    sent += (w + " ")
cnnDoc.append(sent)
```

TF-IDF 행렬과 그에 따른 단어들도 저장해준다.  

```python
cnnTF_IDF = vectorizer.fit_transform(cnnDoc)
with open("cnn-tf-idf.pkl", 'wb') as handle:
    pickle.dump(cnnTF_IDF, handle)

vocabs = vectorizer.get_feature_names()
with open("cnn-terms.pkl", 'wb') as handle:
    pickle.dump(vectorizer.get_feature_names(), handle)
```

with 아래 코드들은 피클파일로 저장해서 나중에 다시 쓰기 위함이다.  


TruncatedSVD 행렬을 계산한다. n_components 는 내가 뽑아낼 주제의 개수이다.  
위 식에서 t라고 보면 된다.  
TruncatedSVD에서 components_ 라는 변수는 문서-주제 행렬이다.  
즉 식에서 VT에 해당한다.  

U, 시그마를 둘 다 쓸 상황이라면  
`from scipy.sparse.linalg import svds` 에서 svds 함수를 쓰거나  
`from numpy.linalg import svd` 에서 svd 함수를 써도 된다.  

이 두 함수는 세 행렬을 모두 반환해준다.  

```python
svd = TruncatedSVD(n_components=8)
svd.fit(cnnTF_IDF)
topics = svd.components_
```

절대값으로 정렬한 후 큰 순으로 출력해준다.  
참고로 문서가 92,579개이고 TfidfVectorizer 에서 단어를 1000개라고 설정했으므로   
TF-IDF 행렬의 shape 를 찍어보면 (92579, 1000) 이라고 나올 것이고  
그 중 components_ 는 주제 x 단어 이므로 (8, 1000) 이 나올 것이다.  
아래 코드는 주제 별로 1000개의 단어 중 절대값 상위 5개만 출력하는 코드이다.  

각 topic은 (1, 1000) 차원의 1행 1000열짜리 벡터이고  
그 1000행짜리 array 를 argsort() 를 거치면 정렬이 되면서 인덱스가 같이 따라가서 그 인덱스 배열이 리턴이 된다.  
즉 인덱스배열에 따라 뒤에서부터 그 인덱스에 해당하는 단어와 값을 출력하는 것이다.  

```python
for index, topic in enumerate(topics):
    print("Topic %d : " % (index+1), end="")
    print([(vocabs[i], topic[i].round(5)) for i in np.abs(topic).argsort()[:-6:-1]])
```

전체 코드는 아래와 같다.    


```python
import re
import pickle
import numpy as np
from nltk.tokenize import word_tokenize
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.decomposition import TruncatedSVD

cnnTF_IDF = None
vocabs = None

vectorizer = TfidfVectorizer(max_features=1000, lowercase=True, stop_words="english")

try:
    cnnTF_IDF = pickle.load(open("cnn-tf-idf.pkl", "rb"))
    vocabs = pickle.load(open("cnn-terms.pkl", "rb"))
    print("There is a tf-idf file")
except FileNotFoundError:
    print("No tf-idf file exists.")
    cnnFile = open("../data/English/raw/cnn/all.txt", "r", encoding='UTF8')
    cnnDoc = []
    cnnTokenized = []
    while True:
        line = cnnFile.readline()
        if len(line) == 0:
            print("File read finished")
            break
        line = re.sub("[^a-zA-Z ]", "", line)
        tokenized = word_tokenize(line)
        for word in tokenized:
            if word == "CNN" or word == "highlight":
                tokenized.remove(word)
        cnnTokenized.append(tokenized)

        sent = ""
        for w in tokenized:
            sent += (w + " ")
        cnnDoc.append(sent)

    cnnTF_IDF = vectorizer.fit_transform(cnnDoc)
    with open("cnn-tf-idf.pkl", 'wb') as handle:
        pickle.dump(cnnTF_IDF, handle)

    vocabs = vectorizer.get_feature_names()
    with open("cnn-terms.pkl", 'wb') as handle:
        pickle.dump(vectorizer.get_feature_names(), handle)

svd = TruncatedSVD(n_components=8)
svd.fit(cnnTF_IDF)
topics = svd.components_

for index, topic in enumerate(topics):
    print("Topic %d : " % (index+1), end="")
    print([(vocabs[i], topic[i].round(5)) for i in np.abs(topic).argsort()[:-6:-1]])
```

출력은 다음과 같이 나온다.  

![image](https://user-images.githubusercontent.com/22045424/74267915-4643f980-4d4a-11ea-91c5-08d1a7cc6b77.png)

사실 TfidfVectorizer 의 옵션으로 min_df 라고 너무 자주 나오는 단어를 제외시킬 수가 있는데  
이걸 처음에 0.5로 했다가 단어가 6개 밖에 안 나오는 참사가 벌어져서 일단 옵션을 default로 넣어놨다. default 는 1이다.  
이 때문에 너무 자주 나오는 단어 : police, said, obama 등이 나오긴 하지만  
그래도 3, 4, 6, 8 주제는 얼추 맞춘 것 같다.  
3번은 미국 정치를 논하는 것 같고, 4번은 누가 봐도 스포츠, 6번은 Travel, 8번은 World 인 것으로 보인다.  
파라미터를 몇 번 조정해봤는데, 어떨 때는 Health 랑 Entertainment 도 보였다.  
잘 튜닝하면 이걸로도 8개의 주제는 얼추 맞출 것 같다.  

이렇게 그 어떠한 튜닝 없이 라이브러리 쓰는 것으로 정답률 50%를 달성할 정도로 쉬운 LSA 구현이었다.  
LSA 의 단점은 그 행렬이 전체 문서를 다 고려해서 계산이 되기 때문에  
문서 하나가 추가가 되면 또 다시 계산을 해야한다는 단점이 있다.  

Michal Campr와 Karel Ježek 교수의 [논문](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.722.6114&rep=rep1&type=pdf)(2015)에 따르면  
Doc2Vec 과 ROUGE1 모델이 문서 요약에 있어서 가장 큰 성능을 보인다고 한다.  
TF-IDF 와 LSA 도 비교대상에 올랐지만 좋은 성능을 내지 못하였다.  
