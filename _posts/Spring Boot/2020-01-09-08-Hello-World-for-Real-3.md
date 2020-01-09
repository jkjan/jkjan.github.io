---
title : Hello World for Real 3
date : 2020-01-09
categories : [Spring]
---

## 3. JUnit

개발자들은 개발을 하면서 내가 생각한 A 라는 일이 내 예상대로 돌아가는지 테스트를 해봐야 한다.  
아무리 고수준의 개발자라도 단순히 Hello World 를 출력하는 게 아니라면 무조건 테스트가 필요하다.  

println 으로 로그를 남발하면서 개발하더라도 분명 내가 예상치 못한 경우가 무조건 존재하기 때문에  
서버를 돌리고 거기다가 직접 테스트하는 것보다, 코드를 작성해서 손쉽게 테스트하고  
예외를 어떻게 처리를 할 지, 또 내가 예상한 값과 실제 결과 값이 다른지 같은지,  
다르면 그 값이 어떻게 나왔는지를 확인해야 한다.  

물론 그게 다르면 왜 다른지 어디서 문제가 생기는지 알아보는 것은 개발자의 몫이다.  
테스트는 대충 힌트는 줄 수 있지만 그걸 어떻게 수정할지는 개발자가 할 일이다.  

그때는 또 다시 로그의 늪으로 돌아가야 할 것이다.  
개발이 다 그런 거 아니겠는가?  

그리고 그 테스트를 다 하는 것보다는 A 를 추가한다거나 뺄 때 어떻게 일어나는지,  
그게 되면 또 B 를 넣을 땐 어떤 일이 일어나는지 단위별로 테스트를 하는 것이 편할 것이다.  

JUnit 은 이렇게 단위로 테스트할 때 쓰는 프레임워크이다.  
프레임워크라고 하면 마음이 편해진다.  
내가 짤 코드가 적다는 뜻과 같다.  
물론 어떻게 돌아가는지 안다는 가정 하에 이 프레임워크를 잘 쓸 수 있다.  

프레임워크이므로 내가 이럴 땐 이렇게 하세요~ 하고 말만 해놓으면  
JUnit 이 알아서 할 것이다.  

그렇다면 이 JUnit에게 내가 원하는 일을 어떻게 말해줄지 알아보자.  

pom.xml 이나 gradle 에 의존성을 추가해준다.  

pom.xml 에는 이렇게 적으면 될 것이다.  

```xml
        ...
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>junit</groupId>
                    <artifactId>junit</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
        </dependency>
        ...
```

gradle  

```gradle
...
dependencies {
    testImplementation('org.springframework.boot:spring-boot-starter-test') {
        exclude module: 'junit'
    }
    testImplementation('org.junit.jupiter:junit-jupiter-api:5.2.0')
    testCompile('org.junit.jupiter:junit-jupiter-params:5.2.0')
    testRuntime('org.junit.jupiter:junit-jupiter-engine:5.2.0')
}

test { 
    useJUnitPlatform() 
}

```

우리는 JUnit5 를 쓴다.  

이제 우리는 JUnit5 를 쓸 수 있다.  

어떻게 쓸까?  
아니, 우리는 무엇을 테스트 해야 할까?  

우리는 일단 Service 를 테스트해야 한다.  
로직은 주로 Service 에서 이루어지기 때문이다.  
그렇다면 HelloService 에 가서 Alt + Tab 을 눌러보자.  

Create test 라는 메뉴가 나온다.  
여기서 엔터를 눌렀을 때 다음 화면이 나오면 성공이다.  

![image](https://user-images.githubusercontent.com/22045424/72053000-8bb77600-3309-11ea-96f2-eecdefeae2ee.png)

여기서 테스트 하고 싶은 멤버 함수를 체크해서 OK 를 누르면  
밑에 test 패키지에 이 Service 를 테스트하는 공간이 만들어진다.  

![image](https://user-images.githubusercontent.com/22045424/72053301-18623400-330a-11ea-83a7-6aca25a87f21.png)

여기서 나는 간단히 create 와 deleteAll, delete 를 테스트하기로 했다.  

create 는 간단하게, id 가 hello 이고 point 가 100 인 Hello 를 집어넣고  
다시 hello 라는 id 로 찾은 값이 100 인지를 테스트하기로 했다.  
id 는 pk 이므로, 만일 데이터 추가가 제대로 이루어졌다면 두 값은 100으로 일치할 것이다.  

deleteAll 은 전체 삭제를 하고 현존 값을 받았을 때 그 사이즈가 0 인지 보면 되는 것이다.  

delete 는 "test" 라는 id 를 가진 값을 넣고 삭제하고, 다시 "test" 라는 id 로 찾았을 때 그것이 반환되지 않으면 된다.  

이 테스트를 진행하기 위한 코드인 HelloServiceTest 는 다음과 같다.  

```kotlin
package com.example.memo.service

import com.example.memo.entity.Hello
import com.example.memo.repository.HelloRepository
import org.junit.jupiter.api.Test
import org.junit.jupiter.api.extension.ExtendWith
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.boot.test.context.SpringBootTest
import org.springframework.test.context.junit.jupiter.SpringExtension

import org.junit.jupiter.api.Assertions.assertEquals
import org.junit.jupiter.api.assertThrows
import java.util.*
import kotlin.NoSuchElementException

@ExtendWith(SpringExtension::class)
@SpringBootTest
class HelloServiceTest(@Autowired private val helloRepository: HelloRepository) {
    @Test
    fun createTest() {
        val hello = Hello("hello", 100)
        helloRepository.save(hello)
        val test = helloRepository.findById("hello")
        assertEquals(test.get().point, 100)
    }

    @Test
    fun deleteAllTest() {
        helloRepository.deleteAll()
        val helloList = helloRepository.findAll()
        assertEquals(helloList.size, 0)
    }

    @Test
    fun deleteTest() {
        helloRepository.save(Hello("test", 100))
        helloRepository.deleteById("test")
        
        /* 두 가지 방법이 다 가능하다.
        assertThrows<NoSuchElementException> {
            helloRepository.findById("test").get()
        }
        */

        assertEquals(helloRepository.findById("test"), Optional.empty<Hello>())
    }
}
```

역시 어노테이션을 알아보자.  
`@ExtendWith(SpringExtension::class)` 은 JUnit 의 확장 기능을 사용하겠다는 것이다.  
여기서는 사실 확장기능을 쓰지 않아서 딱히 필요는 없다.  
그러나 JUnit4 에 없는 기능들을 쓰려면 이 어노테이션을 붙여야 한다고 한다.  
SpringBootTest 는 이 코드가 테스트용이니까 테스트 할 거면 이걸 쓰라고 프레임워크에게 말하는 것이다.  
나머지도 비슷하다.  
AutoWired 는 이 변수를 알아서 집어넣으라는 뜻이다.  

알아서 집어넣는다고? 뭘 어떻게 알고?  

앞의 글에서 IoC 와 DI 를 기억하는가?  
Spring 에서 클래스 하나가 생성되면 Spring Bean Container에 다 기록이 된다.  
그러면 Bean 이 무엇일까? 우리가 Spring 쓰면서 만든 클래스, 객체가 Bean 이다.  
Bean 은 의존성이 주입되면서 재사용 될 수 있는 객체이다.  
우리는 알게 모르게 Bean 들을 만들고 이들을 Container에 저장하고 있던 것이다.  
조립 설명서가 쓰여지는 것이다.  
그리고 Spring Framework 은 프로그램의 구조, 흐름을 다 알고 있기 때문에  
우리가 일일히 조립해주지 않아도 자기 알아서 빌드할 때 Spring Bean Container 에서 객체를 찾아  
이를 알맞은 곳에 삽입하여 의존성을 주입할 수 있다.  

결론적으로 AutoWired 는, 변수를 너가 알아서 넣으라는 뜻이다.  

즉 이걸 지우고 테스트를 하면 테스트 실패가 나긴 커녕 에러가 난다.  
AutoWired 없이 그냥 변수를 선언하면 내가 넣어야 된다. 근데 내가 직접 넣을 부분이 애초에 코드 상에서 보이지 않는다.  
Spring Framework 의 코드는 숨겨져 있기 때문이다.  
Spring 의 전부를 이해하고 알맞은 곳에 직접 HelloRepository 를 넣고 모든 예외처리를 할 자신이 없다면,  
AutoWired 를 통해 Spring 에게 맡기도록 하자.  
어차피 그러라고 만들어놓은 것이다.  

Test 는 이 아래가 Test 할 때 쓰인다는 것을 Spring 에게 알려준다.  

Spring 을 하다 보면 어노테이션을 많이 쓰는데, 그러면서 IoC 와 DI 에 대한 개념을 차차 알게 된다.  

내가 만든 함수는 내가 쓰는 게 아니라 Spring 이 가져다 쓰고 (IoC)  
내가 만든 변수는 내가 가져다 쓰는게 아니라 Spring 이 가져다준다는 것을 이해하자. (DI)  

마지막으로 assertEquals(expected, actual) 은 두 매개변수가 같다고 선언 (assert) 해보는 것이다.  
이게 진짜 맞으면 테스트 성공이고, 아니면 테스트 실패이다.  

assert 에는 많은 종류가 있다. assertThrows 로 어떤 예외를 던지는지 확인할 수도 있다.  
위의 deleteTest 에서 "test" 를 id 로 가지는 값이 없는 것을 확인할 때  
findById 를 해서 그 값이 Optional.null 인지 테스트할 수도 있고,  
혹은 get() 을 했을 때 에러를 무엇을 띄우는지 확인할 수도 있다.  

위 코드를 작성하면 각 함수 앞에 친절하게 실행 버튼이 뜬다.  
맞다고 예상되는 걸 테스트해보면, 밑에 초록색으로 줄이 생기며 테스트에 성공했다고 뜰 것이고  
일부러 틀린 값을 넣고 테스트해보면 빨간색으로 되면서 실제 값을 보여줄 것이다.  

테스트가 잘 되는지를 테스트하면 이제 다음 글에서 AOP 를 적용해보자.  
