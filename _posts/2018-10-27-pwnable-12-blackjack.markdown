---
layout: post
title:  "12. blackjack"
subtitle: ""
date:   2018-10-27 14:26:13 +0900
categories: security
tags : pwnable
---


![1]({{"/assets/img/security/pwnable/12/1.png" | absolute_url}})

블랙잭 게임에서 백만장자에게 플래그를 준다고 한다.

일단 접속해본다.

<br>

![2]({{"/assets/img/security/pwnable/12/2.png" | absolute_url}})

시작 화면이다.

<br>

![3]({{"/assets/img/security/pwnable/12/3.png" | absolute_url}})

이런 화면이 나오고 2번 옵션을 누르면 블랙잭의 룰을 설명해준다.

<br>
![4]({{"/assets/img/security/pwnable/12/4.png" | absolute_url}})

1번을 누르면 위와 같이 나오고 기본 자산은 500달러다.

<br>
![5]({{"/assets/img/security/pwnable/12/5.png" | absolute_url}})

돈을 모두 잃으면 이렇게 나오고 여기서 Y를 누르면 500$에서 다시 시작한다.

게임은 대충 이해가 갔으니 제공해준 URL에 접속해서 소스코드를 확인해봤다.

소스코드를 확인해보니 좀 이상한 곳이 존재했는데..

소스코드가 상당히 길어 대부분 생략하고 그 부분만 가져와서 적어본다.

<br>

![6]({{"/assets/img/security/pwnable/12/6.png" | absolute_url}})

사용자로부터 배팅할 금액을 입력받는 함수이다.

배팅할 금액으로 입력받은 bet 변수가 현재 잔액(cash)보다 큰지 확인한다.

확인하고 잔액보다 클 경우 다시 입력을 요구하는데

문제는 다시 입력받은 배팅 금액을 다시 잔액과 비교하여 체크하지 않고 바로 리턴을 해버리는 것이다...

그러면 bet 금액을 현재 잔액보다 큰 금액을 입력하고

그 후에 입력하는 금액이 내 현재 잔액과 상관 없이 배팅을 하는 것...

한 번만 이기면 될 것 같은데..

<br>
![7]({{"/assets/img/security/pwnable/12/7.png" | absolute_url}})

현재 잔액보다 큰 금액을 입력하고..

<br>
![8]({{"/assets/img/security/pwnable/12/8.png" | absolute_url}})

이겼당

<br>
![9]({{"/assets/img/security/pwnable/12/9.png" | absolute_url}})

잔액이 엄청 커졌고 그 위에 이상한 문장이 출력됐다.

플래그다.