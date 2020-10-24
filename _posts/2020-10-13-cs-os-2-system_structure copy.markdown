---
layout: post
title:  "2. System Structure & Program Execution"
subtitle: ""
date:   2020-10-13- 20:35:27 +0900
categories: cs
tags : os
comments: true
---


#### 컴퓨터 시스템 구조
 - ![1]({{"/assets/img/cs/os/2020-02/1.png" | absolute_url}})
    - *mode bit* : 실행중인게 운영체제인지, 유저 프로그램인지 구분
    - *timer* : 특정 프로그램이 CPU를 독점하지 못하게 timer 값을 설정한 다음에 CPU 할당 
      - 운영체제는 CPU를 선점할 수 없음.. CPU가 자원을 할당할 때 설정하는 timer로 switching 되는 것..
    - *interrupt line* : 매 instruction이 끝나면 interrupt line 확인
      - interrupt가 들어오면 CPU가 운영체제로 넘어감
    - 

- **Mode bit**
  - 사용자 프로그램의 잘못된 수행으로 다른 프로그램 및 운영체제에 피해가 가지 않도록 하기 위한 보호 장치
  - Mode bit을 통해 하드웨어적으로 두 가지 모드의 operation 지원
    - 0 : 모니터 모드 : OS 코드 수행 (= 커널모드, 시스템 모드)
    - 1 : 사용자 모드 : 사용자 프로그램 수행
    - 보안을 해칠 수 있는 중요한 명령어는 모니터 모드에서만 수행 가능한 **특권명령**으로 규정
    - `Interrupt`나 `Exception` 발생시 하드웨어가 `mode bit`을 0으로 바꿈
    - 사용자 프로그램에게 CPU를 넘기기 전에 `mode bit`을 1로 셋팅

- **Device Controller**
  - I/O device controller
    - 해당 I/O 장치유형을 관리하는 일종의 작은 CPU
    - 제어 정보를 위해 `control register`, `status register`를 가짐
    - `local buffer`를 가짐 (일종의 data register)
  - I/O는 실제 `device`와 `local buffer` 사이에서 일어남
  - Device controller는 I/O가 끝났을 경우 `interrupt`로 CPU에 그 사실을 알림
  - 메모리는 CPU, DMA만 접근, 각 디바이스는 `local buffer`를 통해서 메모리 간접 접근
  - `memory controller`는 CPU, DMA의 메모리 접근을 중재

- **입출력(I/O)의 수행**
  - 모든 입출력 명령은 **특권명령** (mode bit : 1)
  - 그럼 사용자 프로그램은 어떻게 I/O 수행?
    - 시스템콜(system call)
      - 사용자 프로그램은 운영체제에게 I/O 요청
    - `trap`을 사용하여 `interrupt vector`의 특정 위치로 이동
    - 제어권이 `interrupt vector`가 가리키는 인터럽스 서비스 루틴으로 이동
    - 올바른 I/O 요청인지 확인 후 I/O 수행
    - I/O 완료 시 제어권을 시스템콜 다음 명령으로 옮김

- **Interrupt**
  - 인터럽트 당한 시점의 `register`와 `program counter`를 저장한 후 CPU 제어를 인터럽트 처리 루틴에 넘김
  - 넓은 의미로 해석하면?
    - `Interrupt` : 하드웨어 인터럽트
    - `Trap`      : 소프트웨어 인터럽트
      - *Exception* : 프로그램이 오류를 범한 경우
      - *System call* : 프로그램이 커널 함수를 호출하는 경우
  - Interrupt vector?
    - 해당 인터럽트의 처리 루틴 주소를 가지고 있음
  - Interrupt service routine? (interrupt handler)
    - 해당 인터럽트를 처리하는 커널 함수
  
- **System call**
  - 사용자 프로그램이 운영체제의 서비스를 받기 위해 커널 함수를 호출하는 것

- **DMA (Direct Memory Access)**
  - 빠른 입출력 장치를 메모리에 가까운 속도로 처리하기 위해 사용
  - **CPU 중재 없이** `device controller`가 device의 `buffer storage`의 내용을 **메모리**에 `block` 단위로 직접 전송
  - 바이트 단위가 아니라 block 단위로 인터럽트를 발생시킴

-------------------------------------------------------------

#### 동기식 입출력과 비동기식 입출력
- 동기식 입출력 (Synchronous I/O)
  - I/O 요청 후 입출력 작업이 완료된 후에야 제어가 사용자 프로그램에 넘어감
  - 구현 방법1
    - I/O가 끝날 때까지 CPU를 낭비시킴 (busy waiting)
    - 매 시점 하나의 I/O만 일어날 수 있음
  - 구현 방법2
    - I/O가 완료될 때가지 해당 프로그램에게서 CPU를 뺏음
    - I/O 처리를 기다리는 줄에 그 프로그램을 줄 세움
    - 다른 프로그램에게 CPU를 줌
- 비동기식 입출력 (Asynchronous I/O)
  - I/O가 시작된 후 입출력 작업이 끝나기를 기다리지 않고 제어가 사용자 프로그램에 즉시 넘어감

- ![1]({{"/assets/img/cs/os/2020-02/2.png" | absolute_url}})

##### -> 두 경우 모두 I/O의 완료는 인터럽트로 알려줌
