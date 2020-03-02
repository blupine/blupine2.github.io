---
layout: post
title:  "2. collision"
subtitle: ""
date:   2018-09-15 17:37:23 +0900
categories: security
tags : pwnable
---

![1]({{"/assets/img/security/pwnable/2/1.png" | absolute_url}})

아빠가 MD5 해시 충돌을 알려줬다고 한다.

이번에도 힌트를 통해 해시 충돌과 관련된 문제임을 알 수 있다.

알려준 주소로 접속해본다.


![2]({{"/assets/img/security/pwnable/2/2.png" | absolute_url}})

1번 문제와 크게 다르지 않은 형식으로 권한이 없는 flag 파일을 setuid가 활성화 된 col 바이너리를 실행하여 읽는 것으로 추정된다.

col.c 파일을 열어 col 바이너리가 어떤 일을 수행하는지 살펴본다.

![3]({{"/assets/img/security/pwnable/2/3.png" | absolute_url}})

첫 번째로 전달받은 argv[1]을 check_password에게 전달하여 호출하고 반환되는 값이 hashcode와 일치할 경우 flag가 출력된다. 

두 번째 if문을 통해 argv[1]의 값은 반드시 20바이트여야 함을 알 수 있다.

Check_password 함수를 확인하면 전달받은 문자열을 int* ip로 가리킨다. 

반복을 돌며 ip의 인덱스를 증가시키며 문자열을 읽고 res에 더한다. 


정리하면 전달받은 문자열을 4바이트 단위로 끊어 모두 더하고 해당 값이 0x21DD09EC와 일치하면 플래그가 출력된다.

간단하게 0x21DD09EC를 5회 나눠서 페이로드를 작성하였고 나누어 떨어지지 않아 0x04 만큼의 손실이 발생하여 

마지막 수는 0x04만큼 더 더하여 작성하였다.

{% highlight bash %}
./col `python -c ‘print “\x06\xC5\xCE\xC8”*4 + “\x06\xC5\xCE\xCC”’`
{% endhighlight %}

페이로드를 입력하여 실행한 결과 패스코드가 일치하지 않음.

리틀엔디안 방식으로 재배치하여 입력

{% highlight bash %}
./col `python -c ‘print “\xC8\xCE\xC5\x06”*4 + “\xCC\CE\xC5\x06”’`
{% endhighlight %}

![4]({{"/assets/img/security/pwnable/2/4.png" | absolute_url}})

-------------------------------------------------------
### pwntools
{% highlight python %}
from pwn import *
 
id   = 'col'
host = 'pwnable.kr'
port = 2222
pw   = 'guest'
s = ssh(id, host, port=port, password=pw)
 
payload1 = 0x06c5cec8
payload2 = 0x06c5cecc
 
payload = p32(payload1)*4 + p32(payload2)
 
 
res = s.run('./col ' + payload)
 
#.sendline(payload)
 
print res.recvall()
 
s.close()
{% endhighlight %}


![5]({{"/assets/img/security/pwnable/2/5.png" | absolute_url}})

