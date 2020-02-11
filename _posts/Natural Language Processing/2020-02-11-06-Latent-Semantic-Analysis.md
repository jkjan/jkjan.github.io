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

