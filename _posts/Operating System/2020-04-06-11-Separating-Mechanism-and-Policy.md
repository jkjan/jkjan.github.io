---
title : Separating Mechanism and Policy
categories : [Operating System]
date : 2020-04-06

---

&nbsp;

## 메커니즘과 정책의 분리

&nbsp;

### 메커니즘/정책 분리의 일상적인 예

잠겨진 문에 접근하기 위해 "카드 키"를 사용하는 예를 들 수 있다.

메커니즘은 입장 정책에 그 어떠한 제한을 두고 있지 않다. 

여기서 입장 정책이란 어떤 사람이 언제 어떤 문에 들어갈 수 있는지를 말한다.

중앙 보안 서버가 이를 결정한다. 

그 말은, 방에 대한 접근 규칙의 데이터베이스를 바꾸어 결정할 수 있다는 말이다.

특정 권한을 결정하는 건 방 접근 데이터베이스를 바꿔서 간단하게 결정할 수 있다.

만일 이 데이터베이스의 규칙이 엄청 제한적이라면, 

근본적인 메커니즘을 바꾸지 않은 채로 남겨지는 동안 

모든 보안 서버가 교체될 수 있다.

방마다 설치된 리더기, 카드를 바꿀 필요가 없다는 말이다.

&nbsp;

이에 반해 물리적인 열쇠를 생각해보자면

누가 문을 열 수 있는지를 바꾸려면 새 열쇠를 만들고 잠금 장치를 바꿔야 한다.

이는 메커니즘이 수정된다는 것을 의미한다.

&nbsp;

이는 잠금 해제 메커니즘과 접근 정책을 엮어버리는 것이다.

호텔이라면 이는 키 카드를 쓰는 것보다 훨씬 덜 효율적이다.

이 문제는 메커니즘과 정책이 구분되지 않아서 생기는 일이다.

&nbsp;

&nbsp;

### 정책에서 메커니즘 분리하기 - 함수 구현

&nbsp;

네 점 (왼쪽, 위, 오른쪽, 아래)의 위치를 받아 넓이를 계산하는 함수를 생각해보자.

&nbsp;

```c++
int rect_area(int left, int top, int right, int bottom) {
    return (right - left) * (top - bottom);
}
```

&nbsp;

구현은 쉬우나, 사용자가 전달하는 네 개의 매개변수에서

 left가 right보다 크거나

bottom이 top보다 큰 경우를 대비해야 한다.

&nbsp;

이 두 경우를 처리하는 함수 `rect_area1`와 `rect_area2` 함수를 작성한다.

&nbsp;

```c
int rect_area1(int left, int top, int right, int bottom) {
    if (left >= right)
        left = right;
    if (bottom >= top)
        bottom = top;
    
    return (right - left) * (top - bottom);
}
```

&nbsp;

이 함수는 직사각형의 넓이를 계산해서 반환하되, 

이상한 값이 들어오면 0을 반환한다.

&nbsp;

```c
int rect_area2(int left, int top, int right, int bottom) {
    if (left >= right || bottom > top)
        return -1;
    
    return (right - left) * (top - bottom);
}
```

&nbsp;

이 함수는 이상한 값이 들어오면 -1를 반환한다.

&nbsp;

이때 `rect_area1`과 `rect_area2`  함수를 비교해보면

직사각형의 넓이를 계산하는 코드가 중복이 되어있다.

이는 딱 한 줄이라서 사실 중복되는 건 별 게 아니다.

하지만 이는 코드 구조상 좋지 않다. 왜 이런 일이 일어난 것일까?

&nbsp;

직사각형의 넓이를 구하는 것은 '메커니즘'이다. 

하지만 이 예에서 오류를 처리하는 것은 '정책'에 해당한다.

함수에 이 둘이 같이 포함되어 있는 것이 문제인 것이다.

&nbsp;

따라서 이 구조에서 메커니즘과 정책을 분리하기 위해서는

다음과 같이 구현되어야 한다.

&nbsp;

```c
static inline int _rect_area(int left, int top, int right, int bottom) {
    return (right - left) * (top - bottom);
}

int rect_area1(int left, int top, int right, int bottom) {
    if (left >= right)
        left = right;
    if (bottom >= top)
        bottom = top;
    
    return _rect_area(left, top, right, bottom);
}

int react_area2(int left, int top, int right, int bottom) {
    if (left >= right || bottom > top)
        return -1;
    
    return _rect_area(left, top, right, bottom);
}
```

&nbsp;

위의 `_rect_area`  함수가 메커니즘, 

`rect_area1`과 `rect_area2`가 각각 정책1, 정책2에 해당한다.

