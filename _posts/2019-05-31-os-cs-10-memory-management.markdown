---
layout: post
title:  "10. Memory Management"
subtitle: ""
date:   2019-05-31 14:24:07 +0900
categories: cs
tags : os
comments: true
---

###Memory Management

용어 정리
- `Frame` 
  - 메인메모리의 고정 길이 블록
- **`Page`** 
  - 보조기억장치에 있는 고정 길이의 데이터 블록. 메인메모리의 프레임에 복사되어서 사용됨
  - HDD, SSD에서 메인메모리로 올 땐 항상 페이지 단위로 오게 됨
- `Segment` 
  - 보조기억장치에 있는 가변 길이의 데이터 블록.
  - 프로그램의 `logcal`한 구성 단위 `e.g) .init, .text, .rodata, .data, .bss`
  - 메인메모리의 가용 공간에 전체 세그먼트가 복사되어서 사용? `(segmentation)`
  - 페이지로 나뉘어져서 개별로 메인메모리에 복사될 수도 있음 `(segmented paging)`
  

####Relocation
- 프로세스 `utilization`을 위해 메인메모리의 프로세스는 `swap-out`, `swap-in`이 가능함
- 이 때, `swap-out` 후에 `swap-in` 할 때 이전에 올라왔던 메모리 주소가 아닌 다른 주소에 배치될 수 있음

####Protection
- 프로세스는 참조된 메모리 영역에 따라서 권한`(read, write, execute)`이 필요함
- `access rights`는 보통 세그먼트 단위로 정해져 있음
  
####Sharing
- 여러 프로세스가 프로그램의 복사본을 각자 유지하는 것은 비효율, 같은 복사본에 대해 접근할 수 있음`(sharing)`

###Memory Organization (구조?)
- 논리적 구조 (Logical Organization)
  - 사용자의 관점에서 프로그램은 모듈(파일)들의 모임(collection) `e.g) 동적 링킹, 라이브러리 파일`
  - 