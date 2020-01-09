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
