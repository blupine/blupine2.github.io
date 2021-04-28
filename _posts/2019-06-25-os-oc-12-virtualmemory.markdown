---
layout: post
title:  "12. Virtual Memory"
subtitle: "Demand Paging/Optimal algorithm/"
date:   2019-06-25 15:24:07 +0900
categories: cs
tags : os
comments: true
---

**운영체제가 개입하는 가상 메모리 관련 기술들**

## Virtual Memory

#### Demand Paging
- 실제로 필요할 때 `page`를 메모리에 올리는 것
  - I/O 양이 감소
  - Memory 사용량 감소
  - 빠른 응답 시간 (전체 System 관점에서의 응답시간, 한정된 메모리를 효율적으로 사용한다는 측면에서)
  - 더 많은 사용자 수용
- `Valid / Invalid bit`의 사용
  - `Invalid`의 의미
    - 사용되지 않는 주소 영역인 경우
    - 페이지가 물리 메모리에 없는 경우
  - 처음에는 모든 `page entry`가 `invalid`로 초기화
  - `address translation` 시에 `invalid bit`가 `set`되어 있으면 -> `Page fault!`
  - `Page fault` 발생 시 디스크에서 `page`를 찾아서 올려줘야 함(I/O 작업) -> 운영체제 개입의 필요

-------------
- **Memory에 없는 Page의 Page Table**
  - ![1]({{"assets/img/cs/os/12/1.png" | absolute_url}})

-------------
- ***Page fault***
  - `invalid page`를 접근하면 `MMU`가 `trap`을 발생시킴(page fault trap)
  - `Kernel mode`로 들어가서 `page fault handler`가 `invoke`됨
  - 다음과 같은 순서로 page fault 처리
```
1. Invalid reference? (e.g bad address, protection violoation) => abort process
2. Get an empty page frame.(빈 메모리 없으면 뺏음 : replace)
3. 해당 페이지를 디스크에서 메모리로 읽어옴
           3-1.  디스크 I/O가 끝나기까지 이 프로세스는 CPU를 preempt 당함(block)
           3-2.디스크 read가 끝나면 page table entry 기록, vaild/invalid bit = "valid"
           3-3.ready queue에 process를 insert -> dispatch later
4. 이 프로세스가 CPU를 잡고 다시 running
5. 아까 중단됐던 instruction을 재개
```
  - **Steps in Handling a Page fault**
    - ![2]({{"assets/img/cs/os/12/2.png" | absolute_url}})
  - Performance of Deamand Paging
    - ![3]({{"assets/img/cs/os/12/3.png" | absolute_url}})

  - ***Free frame이 없는 경우***
    - page replacement
      - 어떤 `frame`을 뺏어올지 결정해야 함
      - 곧바로 사용되지 않을 `page`를 쫓아내는 것이 좋음
      - 동일한 페이지가 여러 번 메모리에 쫓겨났다가 다시 올라올 수 있음
    - replacement algorithm
      - `page-fault rate`를 최소화하는 것이 목표
      - 알고리즘의 평가
        - 주어진 `page reference string`에 대해 `page fault`를 얼마나 내는지 조사
      - `reference string`의 예
        - 1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5

      - **Optimal Algorithm (가장 좋음)**
        - 이상적인 방법 : 앞으로 참조될 페이지들이 어떤 건지 다 알아야 함, 가장 최적의 해
        - `MIN(OPT)` : 가장 먼 미래에 참조되는 `page`를 replace
        - ![4]({{"assets/img/cs/os/12/4.png" | absolute_url}})
        - 미래의 참조를 어떻게 아는가?
          - Offline algorithm (불가능하기 때문에 Offline에서만 적용)
        - 다른 알고리즘의 성능에 대한 `upper bound` 제공
          - 가장 최적의 해임
          - `Belady's optimal algorithm`, `MIN`, `OPT` 등으로 불림


      - **FIFO(First In First Out) Algorithm**
        - 먼저 올라온 page를 우선적으로 쫓아냄
        - ![5]({{"assets/img/cs/os/12/5.png" | absolute_url}})
        - FIFO Anomaly (Belady's Anomaly)
          - `frame`의 수가 많다고 `page fault`가 적어지는 것이 아님 (위처럼 많아질 수 있음)


      - **LRU(Least Recently Used) Algorithm**
        - 가장 오래 전에 참조된 것을 지움
        - ![6]({{"assets/img/cs/os/12/6.png" | absolute_url}})


      - **LFU(Least Frequently Used) Algorithm**
        - 참조 횟수(reference count)가 가장 적은 페이지를 지움 
          - 최저 참조 횟수인 `page`가 여럿 있는 경우
            - LFU 알고리즘 자체에서는 여러 `page`중 임의로 선정한다
            - 성능 향상을 위해선 가장 오래 전에 참조된 `page`를 지우게 할 수 있음
          - 장단점
            - LRU처럼 직전 참조 시점만 보는 것이 아니라 장기적인 시간 규모를 보기 때문에 `page`의 인기도를 좀 더 정확히 반영할 수 있음
            - 참조 시점의 최근성을 반영하지 못함
            - `LRU`보다 구현이 복잡함


      - LRU와 LFU 알고리즘의 구현
        - ![7]({{"assets/img/cs/os/12/7.png" | absolute_url}})
        - LFU는 Minheap을 이용해서 가장 적게 참조된 `page`를 최 상단에 유지 
  
-------------

#### 다양한 캐싱 환경
- 캐싱(cache) 기법
  - 한정된 빠른 공간(=캐시)에 요청된 데이터를 저장해 두었다가 후속 요청시 캐시로부터 직접 서비스하는 방식
  - `paging system` 외에도 `cache memory`, `buffer caching`, `Web caching` 등 다양한 분야에서 사용
- 캐시 운영의 시간 제약
  - 교체 알고리즘에서 삭제할 항목을 결정하는 일에 지나치게 많은 시간이 걸리는 경우 실제 시스템에 적용할 수 없음
  - `Buffer caching`이나 `Web caching`의 경우
    - `O(1)`에서 `O(log n)` 정도까지 허용
  - `Paging system`의 경우
    - `page fault`인 경우에만 `trap`에 의해 OS가 관여함
    - `page`가 이미 메모리에 존재하는 경우 잠조시각 등의 정보를 OS가 알 수 없음
    - ***O(1)인 LRU의 list 조작조차 불가능*** -> 결과적으로 사용이 불가능함

----------

#### Clock Algorithm
- LRU의 근사(approximation) 알고리즘
- LRU, LFU가 실제 환경에 적용되지 못하는 한계가 있기 때문에 실제 적용되는 모델
- 여러 명칭으로 불림
  - `Second chance algorithm`
  - `NUR(Not Used Recently)` 또는 `NRU(Not Recently Used)`
- `Reference bit`을 사용해서 교체 대상 페이지 선정(circular list)
  - `Reference bit`가 0인 것을 찾을 때까지 포인터를 하나씩 앞으로 이동
  - 포인터가 이동하는 중에 `Reference bit`가 `1`인 것은 모두 `0`으로 바꿈
  - `Reference bit`가 `0`인 것을 찾으면 그 페이지를 교체
  - 한바퀴 되돌아와서도`(second chance)` `0`이면 그때에는 replace 당함
  - 자주 사용되는 페이지라면 `second chance`가 올 때 `1`이므로 replace되지 않음
- ![8]({{"assets/img/cs/os/12/8.png" | absolute_url}})
- ***Clock algorithm의 개선 방법***
  - `reference bit`과 `modified bit(dirty bit)`을 함께 사용
  - `reference bit = 1` : 최근에 참조된 페이지
  - `modified bit = 1` : 최근에 변경된 페이지 (I/O 동반하는 페이지 = `backing storage`에 페이지를 덮어써야 함)
  - 가능하면 `modified bit`이 0인 것을 우선적으로 `swap out`하여 I/O 오버헤드가 발생하지 않도록 함
  
  -----------
#### Page frame의 Allocation
- Allocation problem : 각 프로세스에 얼만큼의 `page frame`을 할당할 것인가?
- `Allocation`의 필요성
  - 메모리 참조 명령어 수행시 명령어, 데이터 등 여러 페이지 동시 참조 
    - 명령어 수행을 위해 최소한 할당되어야 하는 `frame`의 수가 있음
  - `Loop`를 구성하는 `page`들은 한꺼번에 `allocate`되는 것이 유리함
    - 최소한의 `allocation`이 없으면 매 `loop`마다 `page fault`
- Allocation Scheme
  - `Equal allocation` : 모든 프로세스에 똑같은 개수 할당
  - `Proportional allocation` : 프로세스의 크기에 비례하여 할당
  - `Priority allocation` : 프로세스의 `priority`에 따라 다르게 할당
- *할당 외에도 replacement도 가능함*
  - **Global replacement** (위의 방식으로 미리 할당해주지 않아도 됨)
    - Replace 시 다른 프로세스에 할당된 `frame`을 뺏어올 수 있음
    - 프로세스별 할당량을 조절하는 또 다른 방식
    - `FIFO`, `LRU`, `LFU` 등의 알고리즘을 global replacement로 사용시에 해당
    - `Working set`, `PFF` 알고리즘 사용
  - **Local replacement**
    - 자신에게 할당된 `frame` 내에서만 replacement
    - `FIFO`, `LRU`, `LFU` 등의 알고리즘을 프로세스 별로 운영시에 해당

-----------

#### Thrashing
- 프로세스의 원할한 수행에 필요한 최소한의 `page frame` 수를 할당 받지 못한 경우 발생
1. `page fault rate`가 매우 높아짐 
2. CPU utilization이 낮아짐 
3. OS는 `MPD(Multiprocessing degree)`를 더 높여야 한다고 판단
4. 또 다른 프로세스가 시스템에 추가됨 `(higher MPD를 위해)`
5. 프로세스 당 할당된 `frame` 수가 더욱 감소
6. 프로세스는 `page`의 `swap in`/`swap out`으로 매우 바쁨
7. 대부분의 시간에 CPU는 한가함
8. `low throughput`

- **Thrashing Diagram**
- ![9]({{"assets/img/cs/os/12/9.png" | absolute_url}})

----------

#### Working-Set Model
- Locality of reference
  - 프로세스는 특정 시간 동안 일정 장소만을 집중적으로 참조한다
  - 집중적으로 참조되는 해당 `page`들의 집합을 `locality set(working-set)`이라 함
- ***Working-set Model***
  - `Locality`에 기반하여 프로세스가 일정 시간 동안 원할하게 수행되기 위해 한번에 메모리에 올라와 있어야 하는 `page`들의 집합을 `Working-Set`이라 정의함
  - `Working-Set` 모델에서는 프로세스의 `Working-Set` 전체가 메모리에 올라와 있어야 수행되고 그렇지 않을 경우 모든 `frame`을 반납한 후 `swap out(suspend)`
  - `Thrashing`을 방지함
  - `Multiprogramming degree`를 결정함
- ***Working-Set Algorithm***
  - `Working-Set`의 결정
    - `Working set window`를 통해 알아냄
    - window size가 `Δ`인 경우
      - Time interval [t<sub>i</sub>-Δ, t<sub>i</sub>] 사이에 참조된 서로 다른 페이지들의 집합을 `Working-Set`으로 함
    - `Working-Set`에 속한 `page`는 메모리에 유지, 속하지 않은 것은 버림 (즉, 참조된 후 Δ 시간 동안 해당 `page`를 메모리에 유지한 후 버림)
  - 프로세스들의 `working set size`의 합이 `page frame`의 수보다 큰 경우
    - 일부 프로세스를 `swap out`시켜 남은 프로세스의 `working set`을 우선적으로 충족시켜줌 **(MPD를 줄임)**
  - `Working Set`을 다 할당하고도 `page frame`이 남는 경우
    - `swap out`되었던 프로세스에게 `working set`을 할당 **(MPD를 키움)**
  - Working size Δ
    - `Working set`을 제대로 탐지하기 위해서는 window size를 잘 결정해야 함
    - Δ값이 너무 작으면 `locality set`을 모두 수용하지 못할 수 있음
    - Δ값이 너무 크면 여러 규모의 `locality set`을 수용
    - Δ값이 ∞이면 전체 프로그램을 구성하는 `page`를 `working set`으로 간주

-----------
#### PFF (Page-Fault Frequency) Scheme
- `Page-fault rate`의 상한값과 하한값을 둔다
  - `Page-fault rate`가 상한값을 넘으면 `frame`을 더 할당
  - `Page-fault rate`가 하한값 이하이면 할당 `frame` 수를 줄임
- 빈 `frame`이 없으면 일부 프로세스를 `swap out`
- ![10]({{"assets/img/cs/os/12/10.png" | absolute_url}})

---------

#### Page Size의 결정
- Page size를 감소시키면
  - 페이지 수 증가
  - 페이지 테이블 크기 증가
  - `Internal fragmentation` 감소 (페이지 안에서 사용이 안되는 부분이 줄어드니까)
  - `Disk transfer`의 효율성 감소
    - `Seek/rotation` vs `transfer`
      - `Seek` : 디스크의 헤드가 이동하는 작업, 시간이 오래걸리기 때문에 한번 이동했을 때 많은 양을 읽어오는 것이 효율적임
  - 필요한 정보만 메모리에 올라와 메모리 이용이 효율적
    - `Locality`의 활용 측면에서는 좋지 않음
- Trend
  - **Larger page size**

메모리 사이즈가 커지고 페이지 사이즈는 그대로면 페이지 테이블의 크기가 늘어나니까 낭비, 그래서 페이지 크기도 키워줘야 함