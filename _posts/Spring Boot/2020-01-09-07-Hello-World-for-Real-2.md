---
title : Hello World for Real 2
date : 2020-01-09
categories : [Spring]
---

### Service

이제 service 패키지 안의 HelloService 클래스를 만들어준다.  



### DAO (repository)

reporitory 패키지를 만들고 안에 HelloRepository 라는 인터페이스를 만든다.  

그리고 안에 이렇게 작성한다.  

```kotlin
package com.example.memo.repository

import com.example.memo.entity.Hello
import org.springframework.data.jpa.repository.JpaRepository
import org.springframework.stereotype.Repository

@Repository
interface HelloRepository : JpaRepository<Hello, String> {      
}
```

이 인터페이스는 어차피 JpaRepository 를 상속받기 때문에  
따로 우리가 만드는 쿼리를 작성할 게 아니라면 안에 뭘 적을 필요가 없다.  
JpaRepository 의 꺽쇠 안에는 Entity 의 클래스명과 pk의 자료형이 들어간다.  
우리는 Hello (id 는 String, point 는 Int) 라는 클래스를 쓰고 그중 pk 인 id 는 String 이므로  
<Hello, String> 이라고 적으면 된다.  

마치 C++ 에서 stl 쓸 때 `vector<int>` 하고 선언하고 이걸로 선언된 변수에 push_back이나 sort를 쓰면 되는 것처럼  
구현된 걸 편하게 쓰기만 하면 된다.  
게다가 요즘 IDE 는, 특히 IntelliJ 는 변수명 뒤에 점만 쓰면 거기서 쓸 수 있는 메소드가 다 나오고  
메소드 이름도 직관적으로 잘 지어놨기 때문에, 필요한 메소드를 따로 검색하거나 하지 않아도 잘 찾을 수 있다.  
정렬이나 찾기, 삭제, 수정 같이 앵간한 것은 다 있기 때문에 이들의 로직을 구현할 필요가 없다.  

이 repository 가 우리의 DAO (Data Access Object) 를 대신한다.  
나중에 분명 우리가 쿼리를 따로 만들어서 쓰게 될텐데, 이때는 DAO 라는 클래스를 다시 분리해서 쓴다고 한다.  

