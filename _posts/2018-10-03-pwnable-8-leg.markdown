---
layout: post
title:  "8. legs"
subtitle: ""
date:   2018-10-03 11:23:13 +0900
categories: security
tags : pwnable
---


![1]({{"/assets/img/security/pwnable/8/1.png" | absolute_url}})

이번 문제는 arm 어셈블리 관련 문제이다.

URL에서 소스코드와 어셈블리 코드를 다운받고 열어봤다.
  
<br>

![2]({{"/assets/img/security/pwnable/8/2.png" | absolute_url}})

`leg.c` 파일이다.

key를 입력받고 key1, key2, key3 함수를 호출한 반환 값들을 모두 더하고 key와 비교한다.

key가 일치하면 플래그가 출력되므로 우리는 key1, key2, key3 함수의 반한 값을 알아내면 된다.

<br>

![3]({{"/assets/img/security/pwnable/8/3.png" | absolute_url}})

key1 함수의 어셈블리 코드이다.

arm에서는 함수를 호출할 때 레지스터를 이용하고

r0~r3 레지스트리가 파라미터 패싱에 사용된 것으로 기억한다. 

예를들면

{% highlight c %}
printf("%d", 3);
{% endhighlight %}


이런 코드를 컴파일하면 x86 어셈블리에서는 스택을 이용해서 파라미터 패싱을 한다.

esp로 스택의 공간을 벌리고 `"%d"`에 해당하는 문자열을 push, 3을 push, 함수의 반환 주소를 push

그러나 arm에서는

r0 레지스터에 `"%d"`, r1 레지스터에 `3`을 넣고 함수를 호출한다. 함수의 반환 주소는 `lr` 레지스터에 저장된다.

함수의 반환 값은 r0 레지스터에 담고 함수가 종료된다.

<br>
`0x0008ce0` 주소의 명령어

 `mov  r0, r3`

여기서 r0 레지스터에 r3의 값을 담게되고

이후 r0에는 어떠한 값을 넣지 않으므로 결과적으로 r3의 값이 함수의 반환 값이다.

r3에서는 바로 위에서 `pc(program counter)`의 값을 담았다.

pc는 다음 실행할 명령어의 주소를 갖고있으므로 `0x0008cdc`의 다음 명령어의 주소인 `0x0008ce0`가 r0에 들어갈 것이다.

<br>
일단 key1의 반환 값은 `0x0008ce0`

<br>
![4]({{"/assets/img/security/pwnable/8/4.png" | absolute_url}})

key2 함수이다.

마찬가지로 가장 마지막으로 r0 레지스터를 건드는 것부터 역추적을 해보면

`0x0008d10`에서 mov  r0, r3 명령어로 r3의 값을 r0로 옮긴다.

r3에는 무엇이 있는지 올라가보면

`0x0008d04`에서  mov r3, pc 명령어로 pc의 값을 r3에 옮기고 바로 다음에

`0x0008d06`에서  adds r3, #4 명령어로 r3에 4를 더한 값을 다시 저장한다.

그 이후는 r3의 값을 변경하지 않는다.

즉 r3에는

`0x0008d04`에서의 pc이므로 `0x0008d06`와 4를 더한 값인 `0x0008d0a`가 담겨있을 것이다.

<br>
key2의 반환 값은 `0x0008d0a`


<br>
![5]({{"/assets/img/security/pwnable/8/5.png" | absolute_url}})

key3 함수이다.

여기서는 `0x0008d28`에서  mov  r3, lr 명령어를 수행하고 이 r3의 값이 다음 명령어에서 r0에 담기고 함는 종료된다.

lr 레지스터는 앞서 말했듯이 함수의 반환 주소를 담고있으므로 main으로 돌아가 함수의 반환 주소를 확인해보면

lr 레지스터의 값을 알 수 있다.

<br>

![6]({{"/assets/img/security/pwnable/8/6.png" | absolute_url}})

main 함수의 어셈블리 코드이다.

key3를 호출하는 부분

`0x00008d7c`에서 key3가 호출되므로 lr 레지스터의 값은 그 다음 명령어인

`0x00008d80`일 것이다

key3의 반환 값 `0x00008d80`

### key1 + key2 + key3 =  0x0008ce0 + 0x0008d0a + 0x00008d80

<br>
![7]({{"/assets/img/security/pwnable/8/7.png" | absolute_url}})

이제 공격을 해보자.

<br>
![8]({{"/assets/img/security/pwnable/8/8.png" | absolute_url}})

안된다.

처음엔 계산을 잘못한 줄 알고 다시했는데.. 그래도 안된다.

이유는 친절하게 여기서 알아볼 수 있다.

http://infocenter.arm.com/help/index.jsp?topic=/com.arm.doc.ddi0084f/ch01s01s01.html

<br>
![9]({{"/assets/img/security/pwnable/8/9.png" | absolute_url}})


몰랐는데.. ARM은 `3-stage pipeline`이란 걸 사용하는데 쉽게 설명하면 명령어를 실행할 때

`Fetch -> Decode -> Execute` 단계를 거친단다.

즉 명령어를 실행할 땐 이미 세 번째 단계인 것이고

다음 명령어는 Decode 단계

그 다음 명령어는 Fetch 단계이다.

즉 PC 계산이 잘못된 것인데.. 이걸 적용해서 PC를 다시 계산해보면


<br>
![10]({{"/assets/img/security/pwnable/8/10.png" | absolute_url}})

PC를 사용했던 key1 함수에서

네모로 표시된 명령어를 수행할 때 PC는 다음 다음 명령어(화살표)를 가리킨다.

따라서 r3에 들어간 pc의 값은 `0x0008ce4`이다.

<br>
![11]({{"/assets/img/security/pwnable/8/11.png" | absolute_url}})


마찬가지로 key2 함수에서

네모로 표시된 명령어를 수행할 때 pc는 화살표 부분이고

따라서 네모 부분에서 r3에 들어간 pc의 값은 `0x0008d08`이고 여기서 4 더한 값인

`0x0008d0c`가 key2의 반환 값이 된다.

다시 계산을 해보면

### key1 + key2 + key3 = 0x0008ce4 + 0x0008d0c + 0x0008d80

<br>
![12]({{"/assets/img/security/pwnable/8/12.png" | absolute_url}})


<br>
![13]({{"/assets/img/security/pwnable/8/13.png" | absolute_url}})
