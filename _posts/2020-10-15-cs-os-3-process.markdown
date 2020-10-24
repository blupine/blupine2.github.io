---
layout: post
title:  "3. Process"
subtitle: ""
date:   2020-10-15- 01:12:27 +0900
categories: cs
tags : os
comments: true
---

#### 프로세스의 개념
 - 실행중인 **프로그램**
 - 프로세스의 문맥(`context`)
   - CPU 수행 상태를 나타내는 하드웨어 문맥
     - `Program counter`
     - `registers`
   - 프로세스의 주소 공간
     - `code`, `data`, `stack`
   - 프로세스 관련 커널 자료 구조
     - `PCB(Process Control Block)` - 커널 주소공간(data)에 있음
     - `Kernel stack` - 프로세스별 kernel stack

 - ![1]({{"/assets/img/cs/os/2020-03/1.png" | absolute_url}})


#### 프로세스의 상태(Process Status)
 - 프로세스는 상태(state)가 변경되며 수행된다
   - `Running`
     - CPU를 잡고 instruction을 수행중인 상태
   - `Ready`
     - CPU를 기다리는 상태(메모리 등 다른 조건을 모두 만족하고 CPU만 기다리는 상태)
   - `Blocked(wait, sleep)`
     - CPU를 주어도 당장 instruction을 수행할 수 없는 상태
     - Process 자신이 요청한 `event`(e.g. I/O)가 즉시 만족되지 않아 이를 기다리는 상태
       - ex) 디스크에서 file을 읽어와야 하는 경우
   - `New`
     - 프로세스가 생성중인 상태
   - `Terminated`
     - 수행(execution)이 끝난 상태
   - ![1]({{"/assets/img/cs/os/2020-03/2.png" | absolute_url}})
