---
title : Concept of Spring 
date : 2020-01-08
categories : [Spring]
---

## Spring 의 개념

이전 글에서 Spring Boot 로 Hello World! 를 출력해봤다.  
정말 쉬운 일이라는 것은 모두가 동의하는 일일 것이다.  

그렇다면 Spring 은 어떻게 움직이는 것일까?  


앞서 프레임워크 (framework) 란,  
사용자가 개발할 때 필요한, 혹은 자주 쓸만한 기능들을 미리 작성해두고 (정렬, 찾기 알고리즘, DB연동 등)  
뿐만 아니라 프로그램의 설계, 흐름까지 갖추게 하는  
재사용 가능한 클래스와 인터페이스의 모임이라고 하였다.  

Spring 은 웹 개발을 위한 프레임워크로,  
사용자가 정보를 넣거나 받으려고 하는 요청을 했을 때  
그 요청을 받아서 무엇을 하고, 어떤 정보를 어떻게 받아와야 하는지에 대한 흐름이 이미 만들어져 있다.  
그 흐름에 대해 알아보도록 하자.  

## Spring 의 흐름

뭐든지 일을 하려면 설계가 필요한데  
웹 서버를 만드는 것도 예외는 아니다.  

세상엔 다양한 설계 방식이 있다.  

MVP, MVC, MVVM 등이 그들이다.  
이 설계 방식(패턴) 중 Spring 은 MVC 패턴을 따른다.  

![image](https://user-images.githubusercontent.com/22045424/71975438-4b97bb00-3257-11ea-86f8-3afc09888d8d.png)


MVC 패턴은 Model - View - Controller 의 약자로  
사용자가 Controller 에게 어떠한 요청을 하면,  
Controller 는 Model 에서 사용자에게 보여줄 정보를 가져와 View 에게 전달하고  
View 는 받은 정보를 사용자 모니터든지 어디든지 해서 보여준다.  
이때 Model, View, Controller 각각은 독립적이며  
독립적이라는 말은 서로가 가지고 있는 정보를 모르고 있다는 뜻이다.  

즉 막무가내로 짜서 뭐 어디 하나만 수정돼도 전체를 다 뒤지는 게 아니라  
View 쪽에서 수정이 일어나서 에러가 발생하면  
View 만 수정하면 된다는 것이다.  
단, 컨트롤러는 둘의 중재자이기 때문에 이 둘에 대한 정보를 안다.  


그렇다면 이 패턴을 따른 Spring 은 어떻게 작동할까?

![image](https://user-images.githubusercontent.com/22045424/71974532-0e322e00-3255-11ea-9379-59959734d60c.png)

1. 사용자가 요청을 보내면  
2. Dispatcher Servlet 이라는 것이 요청을 가로채서 그 요청에 맞는 컨트롤러를 찾는다.  
3. Handler Mapping 은 알맞은 Controller 를 찾아 반환해준다.  
4. Servlet 은 그 Controller 에게 요청을 전달한다.  
5. 처리 결과 (DB가 필요하다면 DB에서 접속 후) 와 이를 표시할 뷰를 Servlet에게 반환한다.  
6. 받은 뷰를 View Resolver에서 찾는다.  
7. Servlet 이 찾으려는 뷰를 반환해준다.  
8. 모델 객체를 뷰에게 전달하고
9. 처리가 완료된 뷰를 얻은 뒤  
10. 이 뷰를 사용자에게 보여준다.  


공식사이트에서 찾은 간단한 이미지는 다음과 같다.  

![image](https://user-images.githubusercontent.com/22045424/71976189-0aa0a600-3259-11ea-8bf3-fa9704a3c608.png)

아래에서 Front Controller 는 Dispatcher Servlet 을 나타내고 있다.  
Servlet 은 자바로 만들어진 서버 기술이다.  




