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
ORM 은 Object Relational Mapping 으로, DB의 relaition (혹은 관계형 데이터)와 객체를 매핑해주는 것이다.  
JDBC 써갖고 막 statement 쓰고 스트링으로 쿼리 일일히 날릴 필요가 없다.  
그쪽이 만들어놓은 함수들 (거의 있을 건 다 있음) 로 알아서 잘 쓰기만 하면 된다.  
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

기본적인 Hello World 프로그램으로 다른 어노테이션은 붙이지 않았다.  
JPA는 어노테이션을 통해 그냥 데이터 삽입 삭제 뿐 아니라 pk 의 자동 증가나 널 확인 등 다양한 기능을 제공한다.  

reporitory 패키지를 만들고 안에 HelloRepository 라는 인터페이스를 만든다.  
