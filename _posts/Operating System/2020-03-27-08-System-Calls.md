---
title : System Calls
categories : [Operating System]
date : 2020-03-27

---

## System Calls

운영 체제가 제공하는 프로그래밍 인터페이스이다.  

보통 고수준 언어로 구현된다.  



사용자 프로그램이 일을 하다가  

운영 체제만이 할 수 있는 일(입출력 장치, 파일 열고 쓰기 등) 을 만나면  

이 System Call 로 운영 체제를 불러 일을 요청한다.  



![image](https://user-images.githubusercontent.com/22045424/77718276-d356bf80-7025-11ea-9fe4-b6617aa69e58.png)

다음은 사용자로부터 복사할 파일 이름과 새로 저장될 파일 이름을 입력 받아  

파일을 열고 내용을 읽어서 사용자가 지정한 경로에 파일을 출력하는 프로그램이다.  



이런 간단한 프로그램에서도 System Call이 사용된다.  



먼저 사용자로부터 입력 파일 이름을 받아야 하는데,  

이때 "파일 이름을 입력해주세요 : " 같은 메시지를 화면에 표시하는데도  

System Call 이 필요하다.  

그와 동시에 사용자의 키보드 입력을 받아들이는 것도 역시 System Call 이 필요하다.  



이 과정을 두 번 반복한 뒤에 (입력 파일 이름, 출력 파일 이름)  

입력 파일을 여는 것도 System Call 이며,  

파일이 없을 시 프로그램이 종료되어야 하는데, 이 종료조차 System Call 에 해당한다.  

출력 파일을 생성하는 것도 System Call,  

만일 중복되는 파일이 있을 경우 프로그램을 종료하는 것도 System Call이다.  

파일을 열고 읽고 쓰는 것도 System Call이다.  

만일 1KB 짜리 문서를 1Byte 씩 읽고 쓴다면  

여기서만 발생하는 System Call 은 2천번 (2K번)이 된다.  

마지막으로 파일을 닫고, 완료 메시지를 출력하고, 종료하는 것까지도 System Call 이다.  



System Call 은 응용 프로그램이 직접 호출할 수 있는 함수 형태로 구축되어 있다.  

System Call 은 직접 사용되기 보다는 주로 고수준의 API 로 접근이 된다.  

여기서의 API는 System Call 보다 높은 수준의 프로그래밍 인터페이스이다.  

프로그램이 API를 호출하면 API가 그에 맞는 System Call 을 호출하게 된다.  

API를 쓸지 System Call을 직접 쓸 지는 개발자의 마음이다.  



윈도우에는 Win32 같은 API,  

유닉스, 리눅스, 맥OS 같은 POSIX 기반 시스템에서는 POSIX API를 사용하고  

자바 가상 머신에서는 자바 API 를 사용한다.  



System Call을 안 쓰고 API를 쓰는 이유는  

같은 API를 사용하는 다른 시스템에서도 잘 컴파일 되고 실행되기 위해서이다.  

POSIX 같은 표준 API의 목적이 바로 이것이다.  

이를 '프로그램의 소스코드 수준의 호환성이 유지된다'라고 한다. 

그리고 System Call 은 API보다 어렵다.  



![image](https://user-images.githubusercontent.com/22045424/77718926-73611880-7027-11ea-9816-d6b31a190cdc.png)



system call은 응용 프로그램이 사용할 수 있는 함수라고 하였다.  

위는 유닉스와 리눅스 시스템에서  

파일이나 장치를 읽어들이는 read system call 의 설명이다.  

기존 함수와 같이 파라미터, 그리고 반환값이 있다.  



## System Call Implementation

![image](https://user-images.githubusercontent.com/22045424/77719070-bc18d180-7027-11ea-8f8e-8f5322ce56a3.png)

위는 System Call 이 발동했을 때의 과정을 그림으로 나타낸 것이다.  

전체적으로 봤을 때,  

System Call -> 처리 -> 복귀 의 과정을 거친다.  



먼저 운영 체제는 듀얼 모드로,  

사용자 모드와 커널 모드가 구분되어 존재한다고 하였다.  

응용 프로그램은 사용자 모드에서 동작한다.  

반면 밑은 커널 모드에서 동작하는 커널 프로그램이다.  



운영 체제가 제공하는 System Call 마다 번호가 부여돼있다.  

그리고 그 번호엔 해당 System Call 이 처리하는 함수의 포인터로 이루어진 테이블이 있다.  

즉 커널의 코드에는 특별한 System Call 을 처리하는 함수가 존재한다.  



이렇게 포인터로 이동해, 처리 함수로 System Call을 한 응용 프로그램의 요청을 처리하게 되는데  

이때 운영 체제의 제어는 처리 함수에게 넘어간다.  



위 그림 속에는 System Call 이 어떻게 이루어지는지 과정이 나타나있다.  



1. 응용 프로그램이 동작하다가 System Call을 호출한다.

2. 이 System Call이 인터페이스를 거쳐 커널의 코드가 실행이 된다.  

   이 과정에서 실행 모드가 사용자 모드에서 커널 모드로 바뀐다.  

   해당 System Call 번호를 가지고 처리 함수의 시작 주소를 찾는다.  

3. 커널 내에서 처리 함수로 제어가 옮겨진다.

4. 처리 함수를 종료 후 복귀한다. 

5. 실행 모드가 다시 사용자 모드로 전환되고, System Call 직전으로 복귀된다.  



## System Call Parameter Passing

그렇다면 System Call 의 함수를 부를 때 매개변수는 어떻게 전달할까?

System Call 은 간단한 정보를 필요로 할 때도 있지만  

System Call 에 따라 종종 더 많은 정보를 원할 때도 있다.  

정보의 종류와 양은 운영 체제와 System Call에 따라 달라진다.  



운영 체제에게 매개 변수를 전달해주기 위해서는  

아래의 세 가지 방법이 사용된다.  



1. 매개변수를 레지스터로 전달하는 것이다.  

   이는 가장 쉬운 방법이지만 

   레지스터보다 매개변수가 많아질 수 있다.

2. 매개변수를 메모리의 한 블록이나 테이블에 저장하고 

   그 주소를 레지스터에 넣어 저장한다.

3. 매개변수를 스택에 push 하고 그걸 운영 체제가 pop 하여 사용한다.



위 방법 중 2, 3번은 매개변수의 개수나 크기에 제약이 없다.  



![image](https://user-images.githubusercontent.com/22045424/77720018-0bf89800-702a-11ea-8078-4dd070a649a7.png)



위 그림에서 사용자 프로그램은 고유번호 13번의 System Call을 부르고 있다.  

그리고 매개 변수가 담긴 사용자 프로그램 공간의 주소인 X를 레지스터에 넣는다.  

운영 체제는 레지스터에 담긴 X 주소로 가 매개 변수를 얻어 13번 System Call 을 실행할 수 있다.  



## Standard C Library Example



![image](https://user-images.githubusercontent.com/22045424/77720150-6134a980-702a-11ea-8e30-975d1c34da0a.png)

```c
printf("Hello World!");
```



C 프로그램은 `printf()` 라이브러리를 호출하는데,   

이는 결국 `write()` 라는 System Call  을 사용한다. 



`printf()`를 쓰는 순간 사용자 모드에서 커널 모드로,  

커널 모드에서 다시 사용자 모드로 바뀐다.



위의 사진에서 `printf()`가 사용되면 C 라이브러리가 이 요청을 가로채서  

`write()`라는 코드를 실행하게 된다.  

System  Call 은 이 함수를 실행하고  

반환값을 라이브러리에게 전달한다. 

라이브러리는 이 반환값을 `printf()`의 적절한 반환값으로 바꿔 전달한다.  



## System Call의 종류

최신 리눅스 커널에서는 System Call은 300가지 이상이다.

System Call은 크게 6가지 범주로 묶을 수가 있다.  

* 프로세스 제어
* 파일 조작
* 장치 조작
* 정보의 유지보수
* 통신
* 보호

등이 그것이다.  



### 프로세스 제어

* 종료, 중지
* 적재, 실행

프로세스는 시작(load,  execute)도 종료(end, abort)도 System Call 을  거친다.  

종료는 정상적, 비정상적 종료를 포함한다.  



* 프로세서 생성, 제거

새 프로세스를 메인 메모리에 적재하고 실행을 개시하고, 

생성하고 종료한다.



* 속성 얻기, 수정

각 프로세스에 대한 정보를 유지한다. 

속성을 얻을 수 있고 (get), 권한이 있다면 일부를 변경할 수도 있다(set).  



* 기다리기

실행을 지연시킬 수 있다. 



* 이벤트나 시그널 이벤트 기다리기

프로세스는 아무것도 안 하고 기다릴 수 있는데,  

이는 일정 시간동안 기다릴 수도 있고,  

어떤 사건이 일어날 때까지 기다릴 수도 있다.  



* 메모리 할당과 반납

메모리를 할당하고 반납한다.



* 오류 생길 시 메모리 dump 하기

프로세스가 실행되다가 오류가 발생하면 프로세스가 종료되면서

그때의 메모리는 파일로 저장이 되는데, 이를 memory dump 라고 한다.

이는 오류를 파악하는데 도움이 된다.



* 디버깅

  디버거는 응용 프로그램의 오류를 찾는 소프트웨어이다.

  프로그램을 실행시키고, 사전에 지정한 시점에서 작동을 멈추게 하는

  **break point** 기능과, 명령이나 한 줄마다 실행시키는

  **single  step** 기능을 보유하고 있다. 

  위 둘은 모두 system call의 도움이 필요하다.



* 잠금

프로세스 간 공유되는 데이터에 대한 접근을 관리(잠금 또는 해제)할 수 있다.

공유 데이터를 잠그면 해당 프로세스만 독점적으로 접근할 수 있다.





### 파일 관리

* 파일 생성, 삭제
* 파일 열기, 닫기
* 읽기, 쓰기, 위치 변경
* 파일 속성 얻기, 수정



### 장치 관리

* 장치 요청, 반환
* 읽기, 쓰기, 위치 변경
* 속성 얻기, 수정
* 논리적으로 장치를 부착 혹은 탈착
* 접근 지점의 변경



시스템의 모든 장치는 자원에 해당한다.

프로세스가 자원을 요청했을 때, 

가용한 자원이 있으면 할당을 받고

없으면 기다려야 한다. 



프로세스가 자원이 필요하면 System Call을 통해 자원을 명시적으로 요청한다.

요청에 의해 할당받은 장치는 사용이 끝나면 반납해야 하는데, 이 역시 System Call을 통해 이뤄진다.

이 점은 파일을 열고(open) 닫는(close) 과정과 매우 비슷하다.

실제로 리눅스는 장치를 파일로 취급한다.



### 정보 유지

* 시간이나 날짜 얻기, 수정
* 시스템 데이터 얻기, 수정
* 프로세스, 파일, 장치 속성 얻기, 수정



많은 운영 체제는 프로그램에 대한 time profile 을 제공한다.

time profile 은 그 프로그램이 특정 메모리에서실행한 시간의 양이다.

이는 tracing 장비나 timer interrupt 가 필요하다.

timer interrupt 발생 시, program counter의 값을 기록하여

timer interrupt가 빈번히 발생했다고 가정했을 때, 

프로그램의 각 부분에서 소비한 시간에 대한 통계를 얻을 수 있다.



### 통신

* 통신 연결 생성, 삭제

* 메시지 전달 모델이라면, 호스트나 프로세스에게 메시지 전달, 

  혹은 그로부터 받기

* 공유 메모리 모델이라면 메모리 지역에 대한 접근을 생성하고 얻기

* 상태 정보 전송

* 원격 장치 부착 혹은 탈착



통신은 메시지 전달 혹은 공유 메모리의 두 가지 방법으로 이루어진다고 하였다.

통신이 이뤄지기 전 System Call에 의해 프로세스 간의 연결이 이뤄져야 한다.

통신이 마무리되어 연결이 끊어지는 것도 역시 System Call 이다.

공유 메모리 모델에서는 한 프로세스가 

다른 프로세스가 공유한 메모리 영역에 대해 접근해야 한다.

이를 위해 공유 메모리를 생성하는 System Call 과, 

생성된 공유 메모리를 자신의 일부로  포함시키는 System Call이 필요하다.



### 보호

운영 체제가 제공하는 자원에 대한 접근을 제어한다.

보호는 다수의  사용자를 가지는 멀티 프로그래밍에서만 고려되는 문제였으나

요즘은 네트워킹과 인터넷의 사용으로, 서버에서부터 휴대용 컴퓨터까지의

모든 시스템에서 보호 문제를 고려해야 한다.



* 자원에의 접근 제어
* 권한 얻기, 수정
* 사용자 접근 허용 또는 거부



권한을 얻는 System Call 과 권한을 설정하는 System Call 로 이루어진다.

파일이나 디스크 같은 자원의 허가 권한을 얻거나

설정하는데에 사용이 된다.

또한 특정 사용자가 지정된 자원의 접근 권한을 

허락할 지 거절할 지를 설정하는 System Call도 있다. 



일부 시스템들은 코드, 그리고 다른 System Call 을 이용하여 

동일한 작업을 실행하는 API를 제공할 수도 있고

일부 시스템들은 단순히 그 작업을 실행하는 프로그램을 제공하기도 한다.

만약 이 시스템 프로그램이 다른 프로그램에 의해 호출 가능하다면

다른 프로그램의 입장에서는 이 시스템 프로그램이 API가 된다.



## Example : MS-DOS



MS-DOS 는 싱글 태스킹이다.  

따라서 하나의 프로세스가 진행되고 있으면, 새 프로세스는 생성되지 않는다.  

메모리 위에는 하나의 프로세스만 올라갈 수 있다. 



따라서 명령 해석기는 이 프로세스에게 최대한 많은 메모리를 보장해주기 위해   

자신의 일부를 프로세스로 덮어쓴다.



프로세스가 시작되면 명령 포인터를 프로그램의 첫번째 명령으로 설정하여

프로그램의 실행을 개시한다.



종류나 오류 발생 시, trap 이 발생하는데,  이때 오류코드가 System에 저장된다.  

이후 명령 해석기 중 덮어쓰이지 않은 부분이 실행 재개되고  

자신의 일부분을 다시 디스크로부터 옮겨와 적재하고

앞의 오류 코드를 사용자나 다른 프로그램이 이용할 수 있게 한다.



![image](https://user-images.githubusercontent.com/22045424/77721089-2e3fe500-702d-11ea-9bc7-651a4b72fc71.png)



위는 MS-DOS의 메모리를 

시스템이 시작했을 때와, 프로그램이 돌아가고 있을 때를 비교한 사진이다.

명령 해석기가 자신의 일부를 희생하므로

오른쪽에서 크기가 일부 줄어든 것을 확인할 수 있다.



## Example : Free BSD

FreeBSD는 유닉스의 일종이고

MS-DOS와 달리 멀티태스킹이 가능하다.

따라서 명령 해석기는 한 프로세스를 실행하더라도 

사용자가 다른 프로세스를 또 실행할 수 있으니

MS-DOS에서완 달리 줄어들지 않고 계속 존재한다.



shell은  `fork()` 명령으로 프로세스를 생성하며  

프로그램을 프로세스로 적재하기 위해 `exec()`를 사용한다. 

`exit()` 코드로 프로세스를 종료하는데,  

이때 상태 코드 또는 오류 코드가 반환이 되어

이를 명령 해석기로 전달한다.

(0일 시 오류가 아니며, 0보다 크면 오류 코드)

이 상태 코드는 shell 이나 다른 프로그램들이 추후에 이용할  수 있다.



shell은 프로세스를 기다릴 수도 있고

사용자의 입력을 계속 받을 수도 있다.  

후자의  경우 사용자는 shell을 통해 다른 프로그램의 실행을 요구하거나

기존 프로그램의 상황을 감시하고

프로그램의 우선순위를 변경하는 등의 요청을 자유롭게 할 수 있다.



![image](https://user-images.githubusercontent.com/22045424/77721411-259bde80-702e-11ea-98bb-c81318de7c3a.png)





## Examples of Windows and Unix System Calls

![image](https://user-images.githubusercontent.com/22045424/77722490-3568f200-7031-11ea-9bc0-c6ceaed1dd7d.png)

위는 Unix와 윈도우즈의 시스템 콜들을 나타낸 것이다.

매개변수등은 생략하였다.



## Process Control

### fork()

새 프로세스를 생성하는 **유일한** 수단이다.



### exit()

종료, 소멸을 하는 시스템 콜이다. 

프로세스의 종료값을 남긴다.



### wait()

다른 프로세스의 종료를 기다린다. 

`wait()`은 보통 자신이 `fork()`을 통해 생성한 프로세스의 종료를 기다릴 때 쓰인다.

`wait()` 를 실행한 프로세스는 실행 중지되며, 지정한 프로세스가 종료될 때에만 다시 실행된다.





## File Manipulation

### open()

파일의 사용 시작을 위해 파일을 개방한다.



### read()

open 된 파일을 읽는다. 

파일의 일정 부분을 프로세스의 메모리 공간으로 복사한다.



### write()

파일을 쓴다.

프로세스의 메모리에 있는 데이터를 파일로 복사한다.



### close()

파일 사용을 마무리한다.





## Device Manipulation

### ioctl()

= i/o control

장치 속성을 읽거나 설정하는 범용 system call이다. 

여기서 범용이란, 모든 장치를 다 이걸로 제어할 수 있다는 말이다.



### read & write()

앞서 말했듯 리눅스 운영 체제는 장치를 하나의 파일로 보기 때문에

File Manipulation 에서의 `read()`와 `write()`와 같은 System Call을 사용한다.



## Information Maintenance

### getpid()

= get process id

프로세스의 ID를 얻는다. 



### alarm()

지정한 시간이 되면 signal을 보낸다.



### sleep()

프로세스를 지정 시간만큼 정지시킨다.





## Communication 

### pipe()

두 개 프로세스 간 단방향 통신 channel 을 만든다.

양방향을 하려면 pipe을 두 개 만들어야 한다.



### shmget()

= shared memory get

공유 메모리를 생성하는 system call이다.



### mmap()

위의 `shmget()`으로 공유 메모리를 만들었다고 바로 사용할 수 있는 게 아니고

생성된 공유 메모리를 자신의 주소 공간에 붙여야 하는데

이때 쓰는 것이 `mmap()`이다.



## Protection

### chmod()

= change mode

파일마다의 사용자 접근 권한을 변경한다.



### umask()

새 파일에 대해 적용되는 권한의 기본값을 변경한다.



### chown()

= changer own

파일의 소유자를 설정한다.
