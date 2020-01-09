---
title : Hello World for Real 2
date : 2020-01-09
categories : [Spring]
---

### Service

서비스는 실제로 코드 작성이 이루어지는 곳이다.  
요청이 들어왔을 때 우리가 무엇을 해야할까?  
이 질문의 답을 Service 라는 클래스에 적으면 된다.  

여기서 그 로직에 따라 DB 접근을 하고, 그 값에 따라서 Service 에서 따로 처리를 한 뒤  
이렇게 처리를 하라고 Controller 에게 다시 보내주는 것이다.  

service 패키지 안에 HelloService 클래스를 만들어준다.  
그러고 다시 HelloController 쪽으로 가면 클래스의 생성자인 HelloService 에 밑줄이 생기고 import 가 가능해질 것이다.  
import 를 해도 밑의 함수들에서 빨간줄이 뜰 것이다.  
왜 그럴까?  
당연히 HelloService 에 해당 함수들이 없기 때문이다.  
그럼 그대로 만들어주기만 하면 빨간줄은 일단 없어질 것이다.  
덤으로 HelloService 에서는 메소드들이 다 노란색으로 뜰 것이다.  
난 노란색 함수가 너무 좋다. 마음이 편안해진다.  

그렇다면 Service 에서는 무엇을 할까?  
예제에선 다섯가지 함수가 있었다.  

`createHello`, `readHello`, `updateHello`, `deleteHello`, `deleteAllHello`, `findRicherThan` 이다.  

각각 생성, 읽기, 수정, 삭제, 전체 삭제이다.  
이들, 특히 앞의 넷은 CRUD, 즉 데이터 처리할 때 가장 기본적인 것들을 일컫는다.  
가장 기본적인 것이라는 것은 나 말고도 누구나 다 쓴다는 말이다.  
당연히 이런 것들은 다 이미 개발이 되어 있다.  
여기서 우리의 JPA 가 등장한다.  

JPA 는 DB 에서 일어날 수 있는 보편적인 일들을 모아놓은 API 이다.  
라이브러리랑은 다르게 언제 어디서 쓰는지에 대한 확실한 목적이 존재한다.  
또한 Interface 로 구현이 되어 있다.  

HelloService 클래스에서는 이렇게 코드 작성을 해준다.  


```kotlin
package com.example.memo.service

import com.example.memo.entity.Hello
import com.example.memo.repository.HelloRepository
import org.springframework.stereotype.Service

@Service
class HelloService(private val helloRepository: HelloRepository) {
    fun createHello (hello : Hello) = helloRepository.save(hello)
    fun readHello(): MutableList<Hello> = helloRepository.findAll()
    fun updateHello(hello: Hello) = helloRepository.save(hello)
    fun deleteHello (id : String) = helloRepository.deleteById(id)
    fun deleteAllHello() = helloRepository.deleteAll()
    fun findRicherThan(point : Int) = helloRepository.findByPointGreaterThan(point)
}
```

이 일곱줄은 당연히 빨간줄이 뜬다. HelloRepository 를 안 만들었기 때문이다.  



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
    fun findByPointGreaterThan(point : Int) : List<Hello>
}
```

이 클래스를 만들면, HelloService에서 뜨던 7개의 빨간 줄이 귀신같이 사라져 있을 것이다.  
HelloRepository 선언에서 빨간 줄이 없어진 건 그렇다 치는데,  
save, findAll, 이런 건 만들지도 안 했는데 어떻게 빨간 줄이 없어진 걸까?  

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

아무것도 쓸 필요 없다면서 findBy... 저건 뭔가?  

눈치챘는가?  
JPA 는 메소드 이름으로 쿼리를 생성해준다.  
이게 진짜 JPA 의 핵심 기능 중 하나라고 생각한다.  
우리는 MySQL 을 쓰면서 MySQL 의 쿼리 문법을 몰라도 된다.  
쿼리가 어떻게 되는지 방식만 이해하면 메소드 이름을 통해 내가 원하는 값을 찾으면 되는 것이다.  
제대로 쓰려면 JPA 도 또한 공부해야 할 것이지만,  
적어도 간단한 선에서는 쿼리를 단 한 줄도 안 쓰고 DB 를 관리할 수 있다.  
즉, DB 를 통째로 바꿔도 properties 랑 dependencies 에 몇 줄만 바꿔주면 여기를 손댈 필요가 없다.  

저 메소드는 point 가 사용자가 입력한 값보다 큰 Hello 들을 찾는 쿼리를 생성한다.  
`"select * from hello where point > $point"` ? 이런 거 안 써도 된다.  

[Spring JPA](https://docs.spring.io/spring-data/jpa/docs/1.4.1.RELEASE/reference/html/jpa.repositories.html)  

물론 이름 짓는데 정해진 규칙은 따라줘야 한다.  
규칙은 위 링크를 좀 내리다보면 나오는 표에 정리되어 있다.  
이름 짓는 규칙도 쉽다.  

이 repository 가 우리의 DAO (Data Access Object) 를 대신한다.  
나중에 분명 우리가 쿼리를 따로 만들어서 쓰게 될텐데, 이때는 DAO 라는 클래스를 다시 분리해서 쓴다고 한다.  

여기까지의 프로그램 구조는 이렇게 이루어질 것이다.  
aspect 패키지는 다음 글에서 다룰 것이니 무시하자.  

![image](https://user-images.githubusercontent.com/22045424/72050786-501aad00-3305-11ea-8c84-4c1a3b5ebc57.png)

다른 빨간 줄이 없고 아까 DB 연동하고 실행했을 때 따로 에러가 안 났다면  
여기까지 하고 실행해도 Hello 라는 클래스를 등록하고 삭제하고 수정하고 다 될 것이다.  
Postman 으로 테스트해보자.  

다음 글에서 Postman 이 아닌 더 개발자답게 테스트하는 방법을 다룬다.  
