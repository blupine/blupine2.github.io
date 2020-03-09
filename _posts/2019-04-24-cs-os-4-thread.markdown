---
layout: post
title:  "4. Thread"
subtitle: ""
date:   2019-04-24 20:35:27 +0900
categories: cs
tags : os
comments: true
---

### Thread?
- User-Level Thread
  - 애플리케이션이 쓰레드를 관리(생성, 소멸, 스케줄링)
  - 커널은 쓰레드 존재 여부를 알지 못함
  - 장점
    - 커널 권한을 요구하지 않기 때문에 context switching이 빠름
    - 스케줄링 알고리즘을 커널과 독립적으로 구성 가능
    - 기존 OS와 상관 없이 작동 가능, 하위 커널에 독립적
    - 정리 : concurrent processing, fast response, multiprocessing, resource sharing
  - 단점
    - 특정 쓰레드가 시스템콜을 호출할 경우 block되어서 모든 쓰레드도 멈춤
    - 따라서 시스템콜을 호출할 땐 장점을 활용 못함
    - 또한 멀티 쓰레드라고 하더라도 한 프로세스엔 하나의 프로세서만 할당 가능, 따라서 장점을 완전히 활용은 불가능
 
- Kernel-Level Thread
  - 커널이 쓰레드 생성, 소멸, 관리
  - 장점
    - 하나의 쓰레드가 시스템콜을 호출해서 block되어도 다른 쓰레드는 실행 가능(concurrency & parallel)
    - OS Kernel 자체도 멀티쓰레딩 가능 - 효율이 좋아짐
  - 단점
    - Context switching 오버헤드가 큼 (커널 레벨로  계속 들어감)
    - 쓰레드 생성 오버헤드가 비교적 큼(커널 레벨로 들어가야 하기 때문) : 유저레벨 쓰레드의 10배
 

- Combined Approach
  - 두 장점을 합친 모델
  - 유저 레벨 쓰레드를 같거나 적은 수의 커널 레벨 쓰레드에 매핑됨
 

- 왜 쓰레드를 쓰냐?
  - 작업 단위가 프로세스일 경우
    - 예를들어 프로세스가 IO를 기다리면 전체 작업의 진행이 멈춤
    - IO를 기다리면서 다른 작업을 하기 위해선 새로운 프로세스를 생성 - 오버헤드 발생
    - 하나의 프로세스를 쓰레드라는 작업 단위로 나누고. 프로세스 자원을 공유하면서 CPU Utilization, 자원 활용을 높일 수 있음 
 

- Application for Multicores
  - Multithreaded native application
    - 여러개의 쓰레드로 구성된 소수의 프로세스로 개발
    - IBM(Lotus) Domino, Oracle(Siebel) CRM(Customer Relationship Manager)
  - Multiprocess applications
    - 싱글 쓰레드 프로세스를 여러개로 구성
    - Oracle DB, SAP
  - Java applications
    - JVM 자체가 싱글 프로세스에 멀티 쓰레드로 구현되어 있음
    - Sun’s Java Application server, IBM’s websphere, J2EE (Java2 Enterprise Edition) application
  - Multi-instance applications
    - 하나의 애플리케이션을 여러 개 실행할 때 별도의 프로세스로 만들어지도록(e.g. 브라우저)

- Multithreading
  - 하나의 프로세스 안에서 여러 개의 쓰레드를 통해 작업 단위를 나누는 것
    - 프로세스는 자원 할당의 단위
    - 쓰레드는 CPU 스케쥴링의 단위
      - 쓰레드의 실행 상태(execution state)는 프로세스보다 제한적임
      - ready, run, blocked 세 상태
      - suspend는 프로세스 단위
      - 쓰레드의 실행 상태를 관리할 수 있는 Thread Control Block이 필요함
        - 쓰레드별로 register context, 실행 스택을 관리 
  - Single-threaded approach
    - 한 개의 프로세스에 한 개의 쓰레드로 이루어진 전통적인 접근 방법
    - 쓰레드의 개념이 없음
    - MS-DOS, old UNIX
  - Multi-threaded appraoch
    - 하나의 프로세스가 여러 개의 쓰레드로 이루어짐
    - Java run-time env
    - 여려 개의 프로세스가 각자 여러 개의 쓰레드로 이루어짐
      - Windows, Solaris, modern UNIX
  - 장점
    - 자원을 공유하기 때문에 새로운 쓰레드 생성이 빠름 (프로세스 생성보다 10배 빠름)
    - 종료 시에는 자원을 release해줄 필요가 없기 때문에 종료도 빠름
    - 쓰레드 간의 Context switching이 빠름
    - 쓰레드 간의 communication 속도가 빠름
      - 프로세스 간의 communication은 시그널을 이용하기 때문에 커널의 개입이 필요
      - 쓰레드 간의 communication은 shared memory를 이용하기 때문에 빠름
    - Faster response
      - 하나의 쓰레드가 block 되어도 다른 쓰레드를 실행이 가능
    - Parallel processing 
      - 멀티코어 시스템에서 여러 프로세서가 여러 쓰레드를 병행 처리가 가능함
      - ![1]({{"/assets/img/cs/os/4/1.png" | absolute_url}})

