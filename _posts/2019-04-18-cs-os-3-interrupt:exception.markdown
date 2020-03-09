---
layout: post
title:  "3. Interrupt / Exception"
subtitle: ""
date:   2019-04-18 18:35:27 +0900
categories: cs
tags : os
comments: true
---

### Interrupt / Exception : 둘 다 예외적인 상황
- Interrupt : 
  - 외부적인 이벤트, 현재 CPU에서 실행되는 프로세스와 상관 없음
  - keyboard interrupt, Direct Memory Access 후 완료를 CPU에게 알리는 상황
  - asynchronous(언제 발생할지 모름), external events
  - 인테럽트가 발생하면 현재 프로세스의 상태를 저장, 인테럽트 핸들러 실행 후 복구
  - 프로세서의 인테럽트 핀이 설정됨
    - #INT
    - #NMI (Non-maskable interrupt) : 우선순위가 #INT 보다 높음 (interrupt disable도 무시)
  - examples
    - I/O interrupt
      - 키보드 ctrl + c 입력, 키보드 입력
      - 네트워크 패킷 수신
      - DMA(Direct Memory Accessing) 완료
    - Hard reset interrupt : reset 버튼 누르기
    - Soft reset interrupt : ctrl + alt + delete
  
  - 정리
    1. 명령어 실행
    2. interrupt 핀 확인
    3. system bus의 interrupt vector 확인
    4. interrupt vector code 실행 (control transfer)
    5. handler returns to next instruction
 

- Exception : 특정 실행중인 프로세스의 CPU가 핸들링할 수 없는 특정 명령어가 발생한 상황(e.g. pagefault) 
  - synchronous(항상 같은 위치에서 동일 조건에서 발생), internal events
  - 종류
    - Traps
      - 의도적인 상황 (명령어 실행을 마치고 의도적으로 멈추는 상황)
      - 명령어 실행을 마치자 마자 발생하게 하는 것
      - ex) debugging, breakpoints, system calls
    - Faults
      - 의도적이지 않은 상황
      - 현재 특정 명령어의 실행을 다 마치지 못함, 명령어 실행이 중단됨
      - NaN, divide by zero 
      - Recover가 가능할 경우 해당 명령어를 다시 실행
    - Aborts
      - 심각한 하드웨어 에러
      - 현재 프로세스를 종료 또는 전체 시스템이 종료될 수 있음
      - MCA(Machine Check Abort), Parity error