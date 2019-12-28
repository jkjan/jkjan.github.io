---
date : 2019-12-29
title : Drawer Layout
categories : [Android]
---

## Drawer

Drawer 는 서랍장이란 뜻이다.  

우리가 앱을 쓸 때 왼쪽이나 오른쪽에서 줄줄이 메뉴가 슥 하고 나오는 경우가 있다.  
보통 왼쪽 상단의 버튼을 누르거나 화면을 왼쪽에서 오른쪽으로 슬라이드하면 나온다.  

메뉴를 서랍장에서 꺼내는 것 같다.  

이 기능의 정확한 명칭은 Navigation Drawer 이다.  


## 구현

구현은 굉장히 쉽다.  

뭐 툴바나 헤더를 다는 포스팅들이 많은데  
현재 개발하는 앱에서는 그것이 필요가 없었다.  
(사실 지금까지의 앱에서도 쓸 필요가 없어 해본 적도 없다.)  


### 1. Drawer Layout  

내가 서랍장을 쓸 곳의 xml 을  
전부 Drawer Layout 으로 감싼다.  
(정확히는 서랍장이 나올 부분이지만, 전체를 감싸는게 개인적으로 더 예쁘다 생각한다.)  


### 2. Relative Layout

Drawable Layout 을 두 부분으로 나눈다.  

서랍장 화면, 그리고 서랍장을 닫은 원래 화면.  
원래 화면을 Relative Layout 으로 구현한다.  
사실 아무거나 다 가능하다.  


### 3. Linear Layout  

서랍장 화면을 Linear Layout 으로 구현  
안에 버튼을 일일히 쓰든  
Recycler View 를 쓰든 자유  
나는 Recycler View 를 썼다.  
주의할 점은, Linear Layout 의 orientation 은 vertical  
layout_gravity 는 start 로 주자.  
layout_width 는 200dp 가 적당하다.  


### 4. onClick

2번의 레이아웃의 상단에 버튼 하나를 달았을 거라 가정  
그 버튼에 리스너를 단다.  

리스너의 안에는 이렇게 쓰면 된다.  

```kotlin
if (!drawer.isDrawerOpen(GravityCompat.START))
    drawer.openDrawer(GravityCompat.START)
else
    drawer.closeDrawer(GravityCompat.START)
```

여기서 drawer는 DrawerLayout 이다.  
뷰 쪽에서 바로 주든, findViewById 로 레이아웃을 얻자.  

### 결과  

```xml
<androidx.drawerlayout.widget.DrawerLayout
        android:id="@+id/drawer"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".activity.MainActivity">
...
  
  <RelativeLayout...
      ...
      android:onClick = "@{()->viewModel.drawerControl(drawer)}"
  </RelativeLayout>
  
  
  <LinearLayout
      android:layout_width="200dp"
      android:layout_height="match_parent"                
      android:layout_gravity="start"
      android:orientation="vertical"
      ... 
  </LinearLayout>
  
  
</androidx.drawerlayout.widget.DrawerLayout>
```

뷰

```kotlin
fun drawerControl(drawer : DrawerLayout) {
    if (!drawer.isDrawerOpen(GravityCompat.START))
        drawer.openDrawer(GravityCompat.START)
    else
        drawer.closeDrawer(GravityCompat.START)
}
```

뷰 모델
