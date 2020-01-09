---
title : Hello World for Real 4
date : 2020-01-09
categories : [Spring]
---

Spring 에는 세 가지 핵심 기능이 있다.  
IoC, DI, AOP 가 그것이다.  

IoC 와 DI 는 다양한 어노테이션으로 우리가 줄곧 사용하고 있다.  
그리고 눈치챘을지도 모르겠지만,  
지금 Controller 의 클래스나 함수에 마우스를 가져다 보면 쓰인 적이 없다고 나온다.  
아니 그럼, 쓴 적도 없는데 서버는 어떻게 돌아간다는 거지?  

우리가 어노테이션을 통해서 서버로 들어오는 행위(GET, POST...) 등과 Mapping 해줬기 때문이다.  
지금 우리가 작성한 HelloController 라는 클래스는 소스파일 어디를 뒤져봐도 없지만,  
빌드하면서 Spring 이 의존성을 주입해 알아서 잘 돌아간다.  
그래서 실제로 쓰인 적이 없다고 하면서 클래스 통째로 회색이 되더라도  
서버는 잘 돌아가는 것이다.  
이것이 바로 Spring Framework 의 기적이다.  
그리고 이를 가능케 하는 것이 IoC 와 DI 이다.  

이렇게 프로그램을 모듈 단위, 객체 단위로 조립하는 것 뿐 아니라  
모듈 안에서도 공통되는 부분을 재사용하기 위해 뺐다가 다른 것들과 조립할 수 있다.  
이를 AOP, Aspect Oriented Programming 이라고 한다.  


이전 글까지만 해도 서버는 벌써 구축이 됐다.  
테스트까지도 가능하다.  
이번엔 Spring 의 핵심 기능을 더 제대로 사용하기 위해  
AOP 를 적용하여 시간을 측정하는 코드를 작성해보자.  

## AOP

프로젝트를 처음 생성할 때를 기억하는가?  
MySQL 과 JPA 등을 선택했고, JUnit 은 따로 넣어주었지만  
AOP 는 그럴 필요가 없다.  
왜냐면 이미 들어가있기 때문이다.  

aspect 패키지에 HelloAspect 라는 클래스를 만들어주자.  

안의 코드는 이러하다.  

```kotlin
package com.example.memo.aspect

import com.example.memo.entity.Hello
import org.aspectj.lang.ProceedingJoinPoint
import org.aspectj.lang.annotation.Around
import org.aspectj.lang.annotation.Aspect
import org.springframework.stereotype.Component
import java.lang.System.currentTimeMillis

@Aspect
@Component
class HelloAspect {
    @Around(value = "execution(* com.example.memo.service.HelloService.createHello(..)) and args(hello)")
    fun aroundAdvice(pjp : ProceedingJoinPoint, hello : Hello) {
        println("Start creating a hello of ${hello.id} with ${hello.point}")
        val tik = currentTimeMillis()
        try {
            pjp.proceed()
        } catch (e : Exception) {
            e.printStackTrace()
        }
        val tok = currentTimeMillis()
        println(String.format("creating a hello done in %.4f seconds", ((tok - tik).toDouble()/1000)))
    }
}
```

개발할 때 있어 오래 걸릴만한 로직 앞 뒤에 시간을 찍어서 그 차를 재거나  
로그를 찍고, DB 를 연결하고, 권한을 얻는 일은 빈번하게 어느 객체에서나 일어나는 일이다.  
그러나 이를 한 번 써놓고 매번 복사 붙여넣기 하는 것은 귀찮은 일이다.  

위 클래스에서 aroundAdvice 라는 함수만 보자.  
tik 으로 현재 시간을 밀리초 단위로 받고,  
pjp 의 proceed 라는 함수를 실행하며 예외처리를 한 후,  
tok 으로 현재 시간을 다시 받아서 두 차를 계산해 이를 출력한다.  

여기서 pjp, ProceedingJoinPoint 클래스의 proceed 함수를 주목하자.  
우리는 여기서 또 Spring 의 기적을 확인할 수 있다.  

바로 그 위의 @Around 어노테이션을 통해 Spring 에게 알려준 메소드들,  
이 경우에는 com.example.memo.service.HelloService 아래의 createHello 함수,
hello 를 매개변수로 하는 함수들 앞, 혹은 뒤에다가 이 함수를 붙여주는 것이다.  

앞의 Aspect 어노테이션으로 Spring 은 원래 그냥 HelloService 의 함수를 실행할 것을,  
이 클래스의 함수를 실행한다. 여기선 aroundAdvice 라는 함수를 실행하는 것이다.  
그리고 그 함수를 실행하면 먼저 시간을 찍고, proceed 라는 함수 - Spring 이 알아서 가져다 준 함수를 실행한 후  
시간 차를 계산해서 출력하는 것이다.  

이 '알아서 가져다 주는' 기능 덕분에 AOP, DI, IoC 가 가능한 것이다.  

이번에는 시간 차를 계산하므로 Around 라는 어노테이션을 썼지만  
이전의 AOP 설명에서도 있듯이  
Before, After, AfterThrowing 등 다양한 어노테이션으로  
내가 재사용할 좋을 코드를 내가 실행할 코드 앞, 뒤, 혹은 앞뒤에 붙일 수 있다.  
그것도 자동으로! Spring 이 알아서 해준다.  

심지어 이는 Spring 이 코드를 알아서 복사 붙여넣기 하는 게 아니라  
지 알아서 aspect 안의 함수를 실행하고, 그 함수 안에서 지 알아서 내가 지정한 함수들을 실행하는 것이다.  
이것이 Spring framework 의 위력이다.  

또한 이 어노테이션 뒤에서 내가 적용하려는 클래스를 바꿀 수도 있다.  
만일 저기서 createHello가 아닌 * 을 썼다면   
HelloService 아래의 Hello 를 매개변수로 하는 모든 클래스에게 적용됐을 것이다.  
나아가 내가 새로 어노테이션을 만들고 특정 메소드 앞에 붙여서   
이 어노테이션이 붙은 함수에선 이를 실행하지 말라고 할 수도 있다.  

Spring 을 통해 Hello 라는 엔터티를 가진 MySQL 을 이용한 DB 에 접속해 이를 관리하고,  
테스트하고, 모듈의 재사용 되는 부분을 따로 빼보는 일을 해보았다.  
IoC, DI, AOP 가 실적용된 예를 알아보고, Spring 이 얼마나 위대한지를 다시 한 번 느꼈다.  

솔직히 내가 한 것은 하나도 없다. 그냥 내가 원하는 기능을 Spring 한테 이야기만 해준 것에 불과하다.  
쿼리를 짠 것도 아니고, 서버와 연결을 한 것도 아니고, 로직을 짠 것도 아니고, 요청을 전달한 것도 아니다.  
아무것도 안 하고 이미 다 만들어진 거 가져다가 연결만 했을 뿐이다.  
좀만 과장하면 DB 모델링만 잘하면 서버는 하루도 안 돼서 만들 수 있을 것 같다.  
이정도로 작업시간과 스트레스를 줄여준 Spring 과 각종 Api 개발자 분들께 감사하다.  
