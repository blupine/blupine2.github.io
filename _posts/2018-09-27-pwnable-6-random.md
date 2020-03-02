---
layout: post
title:  "6. random"
subtitle: ""
date:   2018-09-27 15:55:13 +0900
categories: security
tags : pwnable
---

![1]({{"/assets/img/security/pwnable/6/1.png" | absolute_url}})

random value 문제인가보다

들어가보자
  
<br>

![2]({{"/assets/img/security/pwnable/6/2.png" | absolute_url}})

같은 형식이다.

`random.c`를 읽어보자

<br>

![3]({{"/assets/img/security/pwnable/6/3.png" | absolute_url}})

난수를 발생시키고 난수와 key를 xor한 결과가 `0xdeadbeef`와 일치하면 플래그를 읽을 수 있다.

근데 난수 발생에 그냥 rand 함수를 사용한다.

실제 예측이 불가능한 난수를 발생시키기 위해선 `seed 값`을 줘야 하고

이 `seed 값` 또한 같으면 같은 난수를 발생시키기 때문에

현재 시간을 이용하여 seed를 주고 난수를 발생시키는 방법을 사용한다.


아무튼 정리하면.. 같은 난수가 나오기 때문에 rand 반환 값을 추적하면 답이 나온다..

<br>

![4]({{"/assets/img/security/pwnable/6/4.png" | absolute_url}})

rand를 호출한 직후(+18) bp를 걸고 함수 반환 값을 저장하는 eax 레지스터를 조사한다.

<br>
![5]({{"/assets/img/security/pwnable/6/5.png" | absolute_url}})

답이다.

`0x6b8b4567`

이 값`0x6b8b4567`과 `0xdeadbeef`를 `xor` 연산을 하면 key가 나온다.

<br>
![6]({{"/assets/img/security/pwnable/6/6.png" | absolute_url}})

3,039,230,856

