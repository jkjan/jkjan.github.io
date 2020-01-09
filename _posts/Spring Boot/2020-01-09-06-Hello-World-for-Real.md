---
title : Hello World for Real
date : 2020-01-09
categories : [Spring]
---

이전에 Spring Boot 로 Hello World 를 브라우저 창에 띄우는 것을 해보았다.  
이번엔 진짜로 Hello World 로 도배된 서버를 구축해보자.  

## 목표 

1. MySQL 과 연동하고 Hello 테이블을 만든다.  
2. JPA를 통해 Hello 테이블 (Entity) 에 자료 삽입, 읽기, 수정, 삭제를 구현한다.  
3. JUnit 을 통해 테스트 유닛을 만들어본다.  
4. APO 를 적용해 시간을 측정해본다.  

## 1. MySQL 연동

MySQL 설치는 생략한다.  

커맨드라인이든 워크벤치든 자신의 MySQL DB 를 만들고 안에 Hello 라는 테이블을 생성한다.  

![image](https://user-images.githubusercontent.com/22045424/72032625-424c3400-32d3-11ea-9864-f2a21d4c5fc0.png)

그리고 프로젝트 생성은 이전과 똑같이 하되 이번엔 JPA, MySQL 을 검색해서 추가한다.  

![image](https://user-images.githubusercontent.com/22045424/72032693-89d2c000-32d3-11ea-96d4-0a8677ad89de.png)


좌측에 보면 resources 라는 폴더에 application.properties 라는 문서가 있다.  
이를 수정하자.  

![image](https://user-images.githubusercontent.com/22045424/72032782-c7374d80-32d3-11ea-87e6-19badedfe070.png)

그리고 코드를 다음과 같이 입력한다.  

![image](https://user-images.githubusercontent.com/22045424/72032835-f1890b00-32d3-11ea-9fae-37b802a67686.png)

```
spring.datasource.url=jdbc:mysql://localhost:3306/DB이름?serverTimezone=UTC
spring.datasource.hikari.username=유저네임 (보통 root)
spring.datasource.hikari.password=비밀번호
spring.datasource.hikari.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.hikari.Connection-test-query=SELECT 1
```

여기까지 했으면 일단 메인 함수를 실행하고 시작하자.  
여기서 에러가 나면 문제가 있다. 아마도 properties 문제이니 비밀번호나 이름을 잘 입력한다.  


## 2. JPA 

JPA 는 ORM 의 일종이다.  
ORM 은 Object Relational Mapping 으로, DB의 relation (혹은 관계형 데이터)와 객체를 매핑해주는 것이다.  
JDBC 써갖고 막 statement 쓰고 스트링으로 쿼리 일일히 날릴 필요가 없다.  
그리고 실제로 쿼리를 작성해서 쓰는 것이 아니기 때문에  
DB를 바꾸더라도 전체 코드를 수정하지 않고 쓸 수 있다.  
JDBC 를 통해 "select ... from ... " 이렇게 쿼리를 날렸다면,  
DB 가 바뀌면 아마 그 부분 (DAO) 을 다 갈아엎어야 할 것이다.  

그쪽이 만들어놓은 함수들 (거의 있을 건 다 있음) 로 알아서 잘 쓰기만 하면 된다.  
현재 ORM 에는 JPA 나 MyBatis 등이 있는데,  
여기서는 JPA 를 쓰기로 한다.  

Relation - ORM - Object 인데, 우리는 MySQL 에서 Hello 라는 테이블 (relation) 을 하나 만들었으므로  
이제 Object 를 만들 차례이다.  

entity 라는 패키지 안에 Hello 라는 클래스를 만든다.  

그리고 그 클래스는 다음과 같이 작성한다.  

```kotlin
package com.example.memo.entity

import javax.persistence.*

@Entity
class Hello(
        @Id
        @Column(name = "id")
        val id : String= "def",
        val point : Int = 0)
```

이건 기본적인 Hello World 프로그램이므로 다른 어노테이션은 붙이지 않았다.  
JPA는 어노테이션을 통해 그냥 데이터 삽입 삭제 뿐 아니라 pk 의 자동 증가나 널 확인 등 다양한 기능을 제공한다.  

앞서 Spring 의 구조에서 설명했듯  
유저가 뭔가 요청을 하면 DispatcherServlet 이 그 요청을 알맞은 Controller 에게 전달하고  
그 Controller 가 Service, DAO 를 거쳐서 다시 역순으로 값을 돌려준다.  
(바로 안 하는 이유는 의존성을 줄이기 위함이다.)  

이제 그 각각의 클래스를 만들어보도록 하자.  


### Controller

우리는 Spring Framework 를 사용하고 있다.  
framework 는 단순 library 보다 더 상위의 개념으로  
프로그램의 설계와 흐름까지 제어해준다.  
특히 Spring 은 IoC, 제어의 역전을 가지고 있어  
우리가 코드를 짜고, 그걸 언제 어디서 실행할지 설정할 필요가 없다.  
왜냐면 우리의 코드는 우리가 제어하는게 아니라 Spring 이 알아서 하기 때문이다.  
그냥 손놓고 짜기만 하면 된다.  
그러면 우리는 그 Spring 에게 언제 이 코드를 쓰면 된다 하고 알려줘야 하는데 이걸 어떻게 하면 될까?  

바로 Spring 의 어노테이션이다.  

controller 라는 package 안에 HelloController 라는 클래스를 하나 생성하자.  

그리고 그 안의 코드는 이렇게 적자.  

```kotlin
package com.example.memo.controller

import com.example.memo.entity.Hello
import com.example.memo.service.HelloService
import org.jetbrains.annotations.NotNull
import org.springframework.web.bind.annotation.*
import javax.validation.Valid

@RestController
class HelloController(private val helloService: HelloService) {
    @RequestMapping(path = ["/"])
    fun hello() = "Hello World!"

    @PostMapping(path = ["/"])
    fun createHello(@Valid @NotNull @RequestBody hello : Hello) {
        helloService.createHello(hello)
    }

    @GetMapping(path = ["/"])
    fun readHello(): MutableList<Hello> = helloService.readHello()

    @PutMapping(path = ["/{id}"])
    fun updateHello(@PathVariable("id") id : String, @Valid @NotNull @RequestBody hello: Hello) {
        helloService.updateHello(hello)
    }

    @DeleteMapping(path = ["/{id}"])
    fun deleteHello(@PathVariable("id") id : String) {
        helloService.deleteHello(id)
    }

    @DeleteMapping(path = ["/warning/deleteAll"])
    fun deleteAllHello() {
        helloService.deleteAllHello()
    }
}
```

RestController 라는 어노테이션은  
이 밑의 코드가 RESTful 로 작동한다는 뜻이다.  
이 어노테이션이 있어야 그 밑의 @...Mapping(path = "...") 으로  
행위를, 그리고 그 행위가 이루어질 경로를 찾아가는 것이다.  

POST 는 자료를 등록한다. 블로그 포스팅할 때 그 포스트가 맞다.  
예를 들어 회원가입할 때 다 입력하고 가입 버튼 누르면 POST 로 값을 등록한다.   

GET 은 자료를 받아온다. 서버 들어가면 자료가 바로 뜨는 것들이 다 GET 으로 나오는 것이다.  
노래 사이트 들어가서 자기 앨범 확인하면 즐겨찾기 해놨던 앨범들이 리스트로 촥 뜨는데, 이런 걸 GET 이라고 한다.  

PUT 은 자료를 수정한다. 우리가 무언가 자료를 수정할 때 PUT 행위를 한다.  
회원정보를 수정하고 나서 수정 버튼 누르면 PUT 행위가 일어나는 것이다.  

DELETE 는 자료를 삭제한다. 자료를 삭제하고자 할 때 DELETE 를 한다.  
회원탈퇴가 그 예가 되겠다.  

그리고 그 행위가 일어날 주소를 지정하는데, (http의 인프라)  
코드에 보면 `(path = ["{id}"])` 라고 보인다.  
뒤에 붙을 경로에 id 를 전달해서 그걸로 삭제나 수정이 가능하다.  
예를 들어 .../user/abc 라는 경로에서 DELETE 를 하면 abc 라는 사용자가 삭제될 것이다.  
따로 이용자에게 입력을 받아서 .../user/abc 에서 PUT 을 하면 abc 라는 사용자의 값이 사용자가 입력한대로 바뀔 것이다.  
이 부분에 들어갈 것을 `@PathVariable` 로 선언하고, 뒤에 그 값의 이름과 자료형을 적어준다.  
우리는 DB 를 만들 때 id 라는 이름으로 attribute 를 생성했으므로 이 값은 id 로 하는 것이다.  
다른 쿼리를 쓰는 게 아닌 이상은 보통 pk 로 값을 찾으므로 여기서도 id 로 값을 찾아서 삭제하든 수정하자.  

POST 나 PUT 은 뭘 어떻게 수정할지, 뭘 추가할지를 사용자로부터 받아야 한다.  
따로 입력을 받는 부분을 `@RequestBody` 라는 어노테이션으로 받는다.  
createHello 라는 메소드를 보면 `@Valid @NotNull @RequestBody hello : Hello` 하는 식으로 매개변수를 받는데,  
말 그대로 유효하고 null 이 아닌 `Hello` 클래스를 사용자가 요청한다는 뜻이다.  
만일 이 상태에서 사용자가 null 값을 입력한다면 오류가 발생할 것이다.  
나같은 초보자가 막 짰다면 예상치 못한 곳에서 서버가 터질 것이다.  
하지만 테스트해보니 JPA이나 `@NotNull` 은 초고급 개발자가 짜서 그런지 서버가 터지진 않고 예외처리가 잘 되어있다.  
혹시 모르니 null 값이 들어가면 안 되는 값은 애초에 프론트에서 요청을 보내기 전에 한 번 점검하고  
일단 클래스에서 기본값으로 무언가를 넣어주자.  

지금은 저렇게 쓰면 HelloService 부분에서 바로 빨간 줄이 뜰 것이다.  
글이 너무 길어서 다음 글에서 HelloService 를 만들어보도록 한다.  

