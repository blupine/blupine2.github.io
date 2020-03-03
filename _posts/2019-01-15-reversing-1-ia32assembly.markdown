---
layout: post
title:  "1. IA-32 assemly"
subtitle: ""
date:   2019-01-15 14:40:39 +0900
categories: security
tags : reversing
---


## 1. 레지스터 (Register)
- 프로세서가 사용하는 작은 메모리 공간


1. EAX (Accumulator)
   - 산술 계산에 사용되며 함수의 반환 값을 저장

1. EDX (Data)
   - 산술 계산에 사용되지만 EAX와 같이 함수의 반환 값을 저장하지는 않음



1. ECX (Counter)
   - 주로 반복문의 반복 횟수를 카운팅하는데 사용



1. EBX (Base)
   - Base 값 지정, 주로 index 계산의 Base로 사용

1. ESI (Source)
   - 문자열 또는 각종 반복 데이터를 처리하거나 메모리를 옮길 때 Source 주소 지정을 위해 사용


1. EDI (Destination)
   - 문자열 또는 각종 반복 데이터를 처리하거나 메모리를 옮길 때 Destination 주소 지정을 위해 사용


1. al & ah (Low & High)
   - 8비트 레지스터

<br>

![1]({{"/assets/img/security/reversing/1/1.png" | absolute_url}})
<br>

------

## 2. 어셈블리 (Assembly)

1. PUSH & POP
   - 스택에 값을 넣는 것을 PUSH, 스택의 값을 가져오는 것이 POP (PUSHAD, POPAD : 모든 레지스터를 PUSH, POP)

1. MOV
   - 값을 넣어주는 명령어 (ex : MOV  eax, 1 )

1. LEA
   - 주소를 가져와 넣어주는 명령어

   - #### ※ MOV와 LEA의 차이

   - ![2]({{"/assets/img/security/reversing/1/2.png" | absolute_url}})

<br>
    
4. ADD
   - src에서 dest로 값을 더하는 명령어

1. SUB
   - src에서 dest로 값을 빼는 명령어

1. INT
   - 인테럽트를 발생시키는 명령어. 뒤에 오는 오퍼랜드 값에 해당하는 인테럽트를 발생시킴. 
   - 대표적으로 INT 3 명령어가 존재하며 0xCC opcode를 가진 DebugBreak()가 있다. 

1. CALL
   - 함수를 호출하는 명령어. Call 뒤의 오퍼랜드로 주소가 오게되고 해당 주소를 호출한 뒤 작업이 끝나서 Ret 명령어를 만나면 Call 다음 주소로 되돌아옴

1. INC & DEC
   - 오퍼랜드에 1을 더하거나 뺀다.

1. AND & OR & XOR
   - dest와 src를 연산한다. XOR eax, eax 와 같은 구문은 eax를 초기화하는데 사용

1. NOP
   - 아무것도 수행하지 않고 다음 명령어를 수행하는 명령어

1. CMP & JMP
   - 비교 및 점프 명령어

<br>

---
## 3. 스택 (Stack)
- 프로세서가 사용하는 작은 메모리 공간, 함수의 반환 주소, 파라미터와 함수 내에서 선언하는 지역 변수가 해당 메모리에 위치함

1. 지역변수

   - ![3]({{"/assets/img/security/reversing/1/3.png" | absolute_url}})
   
   - push ebp
    
        이전 함수의 stack base pointer를 저장함 (호출된 함수의 스택 공간을 사용하기 위함)

    - mov  ebp, esp
        
        이전 함수의 esp를 현재 함수의 stack base pointer로 옮김  

    - sub  esp, 50h

        현재 함수의 stack pointer를 50 감소시킴 (0x50 크기의 지역 변수 공간 할당)  

        -> 지역변수가 4바이트 크기라 가정하면 ebp - 4 : 첫 번째 지역변수, ebp - 8은 두 번째 지역변수가 됨
   
<br>

2. 함수호출과 파라미터
      - ![4]({{"/assets/img/security/reversing/1/4.png" | absolute_url}})

      - 위와 같이 3개의 인자를 전달받는 함수가 있을 경우 마지막 인자부터 역순으로 스택에 push되고 함수가 호출됨

      - 따라서 함수 호출 후 ebp에는 이전 스택의 base pointer, ebp + 4에는 함수의 리턴 주소가  위치하고 그 위로는 함수에 전달된 인자가 위치함

      - ebp + 0x8  : 첫 번째 파라미터

      - ebp + 0xC  : 두 번째 파라미터

      - ebp + 0x10 : 세 번째 파라미터


<br>

3. 함수의 리턴 주소
   - 함수가 호출되면 전달된 인자들이 스택에 쌓이고 그 이후에 함수의 리턴 주소가 위치함

   - ![5]({{"/assets/img/security/reversing/1/5.jpeg" | absolute_url}})
