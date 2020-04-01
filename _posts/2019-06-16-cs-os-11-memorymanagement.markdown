---
layout: post
title:  "11. Memory Management"
subtitle: ""
date:   2019-06-16 15:24:07 +0900
categories: cs
tags : os
comments: true
---

###Memory Management

#### Logical address(virtual)
- 프로세스마다 독립적으로 가지는 주소 공간
- 각 프로세스마다 0번지부터 시작
- CPU가 보는 주소는 Logical address임

#### Physical address
- 메모리에 실제 올라가는 위치

#### 주소바인딩
- 주소를 결정하는 것 (Symbolic address -> Logical address -> Physical address)
- `Compile time binding`
  - 컴파일 시 물리적인 주소를 결정하고, 메모리에 올라갈 때 항상 같은 주소로 올라감
- `Load time binding`
  - `Loader`의 책임하에 물리적 메모리 주소 부여
  - 컴파일러가 재배치가능코드`(relocatable code)`를 생성한 경우 가능
- `Execution time binding(=Run time binding)`
  - 실행 이후 프로세스의 메모리 상 위치를 옮길 수 있음
  - CPU가 주소를 참조할 때마다 `binding`을 점검`(address mapping table)`
  - 하드웨어적인 지원이 필요 `e.g. base and limit registers, MMU`

![1]({{"assets/img/cs/os/11/1.png" | absolute_url}})

#### Memory-Management Unit (MMU)
- Logical address를 Physical address로 매핑해주는 Hardware device
- MMU scheme
  - 사용자 프로세스가 CPU에서 수행되며 생성해내는 모든 주소값에 대해 `base register(=relocation register)`의 값을 더해준다.
- User program
  - Logical address만을 다룬다
  - 실제 physical address를 볼 수 없으며, 알 필요가 없다.

------------------
**Dynamic Relocation**
![2]({{"assets/img/cs/os/11/2.png" | absolute_url}})


------------------
**HardwareSupport for Address Translation**
![3]({{"assets/img/cs/os/11/3.png" | absolute_url}})

#### Dynamic Loading
- 프로세스 전체를 메모리에 미리 다 올리는 것이 아니라 해당 루틴이 불려딜 때 메모리에 올리는`(load)` 것
- `Memory Utilization`의 향상
- 자주 사용되지 않는 코드들에 유용 `e.g. 예외 처리 루틴`
- 운영체제의 특별한 지원 없이 프로그램 자체에서 구현 가능 (OS는 라이브러리를 통해 지원 가능)

#### Overlay
- 메모리에 프로세스의 부분 중 실제 필요한 정보만을 올림
- 프로세스의 크기가 메모리보다 클 때 유용
- 운영체제의 지원 없이 사용자가 구현
- 작은 공간의 메모리를 사용하던 초창기 시스템에서 수작업으로 프로그래머가 구현
  - `Manual overlay`
  - 매우 복잡함

#### Swapping
- 프로세스를 일시적으로 메모리에서 `backing store`로 쫓아내는 것
- `backing store`
  - 디스크
    - 많은 사용자의 프로세스 이미지를 담을 만큼 충분히 빠르고 큰 저장 공간
- `Swap in` / `Swap out`
  - 일반적으로 `mid-term scheduler(swapper)`에 의해 `swap out` 시킬 프로세스 선정
  - 우선순위 기반 CPU 스케줄링 알고리즘
    - `priority`가 낮은 프로세스를 `swap out`
    - `priority`가 높은 프로세스를 메모리에 올려 놓음
  - `Compile time binding`, `Load time binding`에서는 원래 메모리 위치로 `swap in` 해야 함
  - `Run time binding`에서는 추후 빈 메모리 영역 아무 곳에나 올릴 수 있음
  - `swap time`은 대부분 `transfer time`임 (swap되는 양에 비례하는 시간)

#### Dynamic Linking
- Linking을 실행 시간까지 미루는 기법
- `Static Linking`
  - 라이브러리가 프로그램의 실행 파일 코드에 포함됨
  - 실행 파일의 크기가 커짐
  - 동일한 라이브러리를 각각의 프로세스가 메모리에 올리므로 메모리 낭비가 큼
- `Dynamic Linking`
  - 라이브러리가 실행시 연결(link)됨
  - 라이브러리가 호출 부분에 라이브러리 루틴의 위치를 찾기 위한 `stub`이라는 작은 코드를 둠
  - 이미 메모리에 있으면 해당 루틴으로 가고, 없으면 디스크에서 읽어옴
  - 운영체제의 도움이 필요


### 물리적 메모리 할당
- 메모리는 일반적으로 두 영역으로 나뉘어 사용
  - `OS 상주 영역`
    - interrupt vector와 함께 낮은 주소 공간 사용
  - `사용자 프로세스 영역`
    - 높은 주소 영역 사용

- 사용자 프로세스 영역의 할당 방법
  - `Contiguous allocation`
    - 각각의 프로세스가 메모리의 연속적인 공간에 적재되도록 함
    - `Fixed partition allocation`, Variable partition allocation
  - `Noncontiguous allocation`
    - 하나의 프로세스가 메모리의 여러 영역에 분산되어 올라갈 수 있음
      - `Paging`, `Segmentation`, `Paged segmentation`

### Contiguous Allocation
- 고정 분할(Fixed Partition) 방식
  - 물리적 메모리를 몇 개의 영구적 분할(partition)로 나눔
  - partition의 크기가 모두 동일한 방식과 서로 다른 방식이 존재
  - partition당 하나의 프로그램 적재
  - 융통성이 없음
    - 동시에 메모리에 올라오는 프로그램의 수가 고정됨
    - 최대 수행 가능 프로그램 크기가 제한됨
  - `Internal fragmentation` 발생 `(external fragmentation도 발생)`
-  가변 분할(Variable partition) 방식 
   -  프로그램의 크기를 고려해서 필요한 만큼안 할당
   -  Partition의 크기, 개수가 동적으로 변함
   -  기술적 관리 기법 필요
   -  `External fragmentataion`이 발생

- `Hole`
  - 가용 메모리 공간
  - 프로세스가 도착하면 수용가능한 `hole`을 할당
  - 운영체제는 다음의 정보를 유지
    - `할당 공간`, `가용 공간(hole)`
  
- **Dynamic Storage-Allocation Problem**
  - 가변 분할 방식에서 `size n`인 요청을 만족하는 가장 적절한 `hole`을 찾는 문제
  - `First-fit`
    - `size n` 이상인 것 중 최초로 찾아지는 `hole`에게 할당
  - `Best-fit`
    - `size n` 이상인 가장 작은 `hole`을 찾아서 할당
    - `hole`들의 리스트가 크기순으로 정렬되지 않은 경우 모든 `hole`의 리스트를 탐색해야 함
    - 많은 수의 아주 작은 `hole`들이 생성됨
  - `Worst-fit`
    - 가장 큰 `hole`에 할당
    - 모든 리스트를 탐색해야 함
    - 상대적으로 큰 `hole`들이 생성됨

- `**Compaction**`
  - `external fragmentation` 문제를 해결하는 한 가지 방법
  - 사용 중인 메모리 영역을 한군데로 몰고 `hole`들을 다른 한 곳으로 몰아 큰 `block`을 만드는 것
  - 매우 많은 비용이듬
  - 최소한의 메모리 이동으로 `compaction`하는 것은 매우 복잡한 문제
  - `compaction`은 프로세스의 주소가 실행 시간에 동적으로 재배치 가능한 경우에만 수행될 수 있음

![4]({{"assets/img/cs/os/11/4.png" | absolute_url}})

------------

### Noncontiguous Allocation
- **Paging**
  - 프로세스의 가상메모리를 동일한 사이즈의 `page` 단위로 나눔
  - 가상메모리의 내용이 `page` 단위로 `noncontiguous`하게 저장됨
  - 일부는 `backing storage`에, 일부는 `physical memory`에 저장
  - *basic method*
    - `physical memory`를 동일한 크기의 `frame`으로 나눔
    - `logical memory`를 동일 크기의 `page`로 나눔(`frame`과 같은 크기, 일반적으로 `4kb`)
    - 모든 가용 `frame`들을 관리
    - `page table`을 사용하여 `logical address`를 `physical address`로 변환
    - `External fragmentation` 발생 안함
    - `Internal fragmentation` 발생 가능 (프로세스의 크기가 `frame`의 크기의 배수가 아닐 경우에는 항상 한 `frame`에 Internal fragmentation 발생)
  - ***Implementation of Page Table***
    - `Page table`은 main memory에 상주
    - `Page-table base register(PTBR)`가 `page table`을 가리킴
    - `Page-table length register(PTLR)`가 테이블 크기를 보관 
    - 모든 메모리 접근 연산에는 2번의 `memory access`가 필요(`page table` 1번, `data/instruction`접근 1번)
    - 속도 향상을 위해 `associative register` 또는 `Translation look-aside buffer(TLB)`라 불리는 고속의 하드웨어 캐시 사용
  - *매번 TLB 전체를 탐색하는 것은 비효율, `parallel search`가 가능한 `Associative registers`를 이용해서 구현*
    - `page table`의 일부가 `associative register`에 보관되어 있음
    - 만약 해당 `page #`가 `associative register`에 있는 경우 곧바로 `frame #`을 얻음
    - 그렇지 않은 경우 `main memory`에 있는 `page table`로부터 `frame#`을 얻음
    - `TLB`는 `context switch` 때 `flush`됨 
  - **Effective Access Time**
    - Associative register lookup time = $$\epsilon$$
    - Memory cycle time = 1
    - Hit ratio = $$\alpha$$ (associative register에서 찾아지는 비율)
    - Effective Access Time(EAT)
      - $$EAT = (1 + \epsilon)\alpha + (2 + \epsilon)(1 - \alpha)
              = 2 + \epsilon - \alpha$$
            

**Paging Example**
![5]({{"assets/img/cs/os/11/5.png" | absolute_url}})

**Address Translation Architecture**
![6]({{"assets/img/cs/os/11/6.jpg" | absolute_url}})

**Paging Hardware with TLB**
![7]({{"assets/img/cs/os/11/7.png" | absolute_url}})

---------

- Two-Level Page Table
  - 현대의 컴퓨터는 `address space`가 매우 큰 프로그램도 실행 가능하도록 지원
    - `32bit address` 사용시 : 2<sup>32</sup>B(4GB)의 주소 공간 
      - `page size`가 4K일 때 1M개의 `page table entry`가 필요함
      - 각 `page entry`가 4B이면, 프로세스당 4M의 `page table`이 필요
      - 그러나 대두분의 프로그램은 4G의 주소 공간 중 지극히 일부분만 사용하므로 `page table`의 공간 낭비가 심해짐 *(사용되지 않는 page가 있더라도, page table이 처음부터 순차적으로 인덱싱이 가능하도록 하기 위해서는 모든 page들이 테이블에 있어야 함)*

      -> `page table` 자체를 `page`로 구성

  - Example
    - Logical address의 구성
      - 20bit의 `page number`
      - 12bit의 `page offset`
    - `Page table` 자체가 `page`로 구성되기 때문에 `page number`는 다음과 같이 나뉨
      - 10bit의 `page number` (outer page table)
      - 10bit의 `page offset` (inner page table)
      - ![8]({{"assets/img/cs/os/11/8.png" | absolute_url}})
      - `page offset` : 4kb인 페이지의 offset을 표현하기 위해 12bit
      - `inner page number(P2)` : 페이지 크기와 동일한 4kb 내부에 4byte의 엔트리들이 있음(총 1024개), 따라서 10bit 필요(총 Inner Page T)
      - `outer page number(P1)` : `inner page`는 한 `page`당 1024개의 `physical memory`의 `frame`을 가리킬 수 있음,`physical memory`의 총 `frame` 수는 2<sup>20</sup>(32bit 기준)이므로 `inner page`는 1024개가 필요, 따라서 1024개를 가리키기 위해 `outer page number`도 10bit 필요
    - ![9]({{"assets/img/cs/os/11/9.png" | absolute_url}})
    - ![10]({{"assets/img/cs/os/11/10.png" | absolute_url}})

- ***결과적으로 두번 참조하기 때문에 시간적으로, 공간적으로 손해인데 왜 Two-Level을 사용할까?*** 
  - 이전의 Single-Level에서는 사용되지 않는 `page`(주소공간)에 대해서도, `page table`의 인덱싱을 위해서 `page table`에 추가해줘야 했음
  - 그러나 Two-Level에서는 사용되지 않는 `page`에 대해서는 `Outer page table`의 엔트리 값을 `NULL`로 해두면 이러한 낭비를 없앨 수 있음(대응하는 `inner page table`이 없음)

     
paging ? - 주소 변환을 페이지별로 올라간 주소를 알고있어야 하기 때문에, 주소변환이 복잡해짐
segmentation? - 의미있는 단위로 쪼갬 code, data, statck 


