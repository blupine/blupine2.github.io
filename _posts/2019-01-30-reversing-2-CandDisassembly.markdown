---
layout: post
title:  "2. C문법과 디스어셈블리"
subtitle: ""
date:   2019-01-30 16:25:39 +0900
categories: security
tags : reversing
---


## 1. 함수 호출 규약
- 함수가 호출될 때 인자를 전달하거나 함수의 결과를 반환하는 방법에 대한 규약

- 대표적으로 __cdecl, __stdcall, __fastcall, __thiscall 네 가지 존재

- 분석 과정에서 해당 함수의 호출 규약을 판단하여 전달하는 인자 및 반환 값을 식별하는 것이 중요

1. __cdecl

   - ![1]({{"/assets/img/security/reversing/2/1.png" | absolute_url}})

   - main 함수에서 __cdecl 호출 규약을 사용한 sum 함수를 호출하는 부분인 call calling.00401000 

    - 해당 함수 호출 이후 add esp, 8과 같이 스택을 보정해주는 명령어가 존재할 경우 __cdecl 규약

    - 또한 해당 스택의 크기를 이용하여 파라미터의 개수까지 확인이 가능, 파라미터는 4바이트씩 계산되므로 위 함수에서는 총 2개의 파라미터가 있음을 파악 가능

1. __stdcall
   - 산술 계산에 사용되지만 EAX와 같이 함수의 반환 값을 저장하지는 않음
   - ![2]({{"/assets/img/security/reversing/2/2.png" | absolute_url}})
   - 함수가 반환하는 ret 명령어에 오퍼랜드로 8이라는 상수가 온다
   - __cdecl 규약은 함수 반환 후에 해당 함수를 호출한 부분에서 스택을 조정하지만 __stdcall 규약을 사용할 경우 호출된 함수 내에서 반환할 때 스택을 조정함, 해당 값을 통해서도 파라미터의 개수를 추측이 가능함
   - Win32 API는 __stdcall 호출 규약을 사용함. MessageBoxA() 함수를 살펴보면 다음과 같음.
   - ![3]({{"/assets/img/security/reversing/2/3.png" | absolute_url}})
   -  해당 함수의 반환 부분에서 총 0x10 만큼의 스택을 재조정해주는 것을 확인할 수 있음

      따라서 해당 함수는 총 4바이트 크기의 파라미터를 4개 (0x10) 전달받을 것이라고 추측이 가능하며 실제 함수를 확인해보면 다음과 같이 일치함을 확인할 수 있음
   - ![4]({{"/assets/img/security/reversing/2/4.png" | absolute_url}})

1. __fastcall
   - ![5]({{"/assets/img/security/reversing/2/5.png" | absolute_url}})
   -  sub esp, 0xC 명령어로 스택 공간을 확보하고 edx, ecx 레지스터에 있는 값을 이용한다.
   -  함수의 호출부를 봐도 함수를 호출하기 직전 파라미터를 edx, ecx 레지스터에 넣는다.
    - __fastcall 호출 규약은 파라미터가 2개 이하일 경우에 사용이 가능하며 파라미터를 레지스터를 이용하여 전달한다. (빠름)

2. __thiscall
   - ![6]({{"/assets/img/security/reversing/2/6.png" | absolute_url}})
   - c++에서 객체의 멤버에 접근하기 위한 this 포인터(객체에 대한 포인터)를 edx레지스터에 전달한다.

<br>

------

## 2. if 조건문
- C 코드의 조건문이 디스어셈블되면 다음과 같은 형태를 가진다.

계속...