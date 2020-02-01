---
title : Loss Function
categories : [Deep Learning]
date : 2020-01-31
---

# 손실 함수

퍼셉트론과 신경망의 다른 점은  
퍼셉트론에서는 가중치를 인간이 직접 매번 입력해주어야 하지만  
신경망은 처음에만 입력해주면 몇 가지 도구를 이용하여 스스로 값을 의미있게 바꾼다는 것이다.  

여기서 '의미있게' 란  
신경망의 출력이 점점 정답에 다가가도록 하는 것을 의미한다. (학습)  
이를 위해 손실 함수를 도입한다.  

손실 함수는 신경망의 출력과 정답을 인수로 가진다.  
함수 안에서 출력과 정답을 비교하여 신경망의 출력이 정답에 **얼마나 가까운지**를 출력한다.  

손실 함수에는 평균 제곱 오차 (mean squared error), 교차 엔트로피 오차(cross entropy error)가 있다.  
평균 제곱 오차는 다음과 같은 식을 가진다.  

![mean_squared_error](https://mblogthumb-phinf.pstatic.net/MjAxNzA2MTBfMjk3/MDAxNDk3MDc3MTMwOTQ2.GgGlorZevi3xnKcBFHqCrG6JKGaWMa-IvVv-927bzecg.8JF52k5hIgKhdEbkzcoo_yPW6Hac3WIucgThhTGvFnsg.JPEG.wideeyed/MSE_formula.jpg?type=w2)

다음은 파이썬으로 구현한 평균 제곱 오차이다. 인수는 ndarray 로 받는다.  

```python
def meanSquaredError(y, t) :
  return np.sum((y-t)**2)/2
```
