---
layout: post
title:  "8. Concurrency : Deadlock and Starvation"
subtitle: "deadlock/starvation"
date:   2019-05-20 22:55:07 +0900
categories: cs
tags : os
comments: true
---

### Deadlock
- 하나 이상의 프로세스들이 다른 블락(blocked)된  프로세스가 trigger 할 수 있는  어떤 이벤트 혹은 리소스를 기다리고 있는 상황
- **리소스(resource)**?
  - `Reusable resource`
    - 한번에 하나의 프로세스에서만 안전하게 사용될 수 있는 자원(소모되지 않음)
      - e.g) `processors`, `memory`, `I/O devices`, `files`, `databases`, `semaphores`
  - `Consumable resource`
    - 생성되거나 소모될 수 있는 자원
      - e.g) `interrupts`, `signals`, `messages`, `data in I/O buffers`

------------------

### Deadlock 발생 조건
- **Mutual Exclusion**
  - 자원이 Mutual Exclusion이 보장되어야 할 때
- **Hold and wait**
  - 자원을 가지고 있으면서 다른 자원을 요청할 때
- **No preemption**
  - 프로세스가 가지고 있는 자원을 개입해서 뺏을 수 없음
- **Circular wiat**
  - 서로 필요로 하는 자원들을 가진 상태가 원형 체인(chain) 형태로 이루어지는 것

위 세가지(Mutual exclusion, hold and wait, no preemption)은 필요 조건, 이 상태에서 circular wait을 할 경우 deadlock 발생함 

### 세가지 접근 방법
- Deadlock prevention
  - 위의 발생 조건 중 한개를 없애서 데드락 발생 가능성을 없애는 것
- Deadlock avoidance
  - 현재 상황에 따라 동적으로 자원을 할당하며 데드락을 피하는 행위
- Deadlock detection 
  - 데드락이 발생하도록 두고, 발생할 경우 탐지하여 회복시키는 것

![1]({{"assets/img/cs/os/8/1.png" | absolute_url}})

#### Deadlock Prevention
- Mutual exclusion
  - 해결 방법이 없음
- Hold and wait?
  - 자원을 all or nothing 방식으로 할당함
- No preemption
  - 프로세스가 자원 할당에 실패하면 할당된 자원을 모두 뺏음
- Circular wait
  - 리소스에 순서를 주고(ordering) 그 순서대로만 자원 할당이 가능하도록 함

#### Deadlock Avoidance
- 현재 자원 할당이 미래에 데드락을 발생시킬 잠재적인 위험이 있는지 판단
- 접근 방법
  - Process initiation denial
  - Resource allocation denial (핵심) - `banker's algorithm`
    
- 장점
  - deadlock prevention 보다 제한적이지 않음
  - 프로세스를 preemption, rollback 할 필요 없음

{% highlight c %}
/* Deadlock Avoidance Logic - banker's algorithm */
struct state{ /* global data structures */
  int resource[m];
  int availale[m];
  int claim[n][m];
  int alloc[n][m];
}

boolean safe(state S){
  int currentavail[m];
  process rest[<number of processes>];
  currentavail = available;
  rest = {all processes};
  while(possible){
    <find a process P in rest such taht 
      claim [k, *] - alloc[k, *] <= currentavail;>
      if(found){ /* 만약 위 조건을 만족하는 프로세스 P가 있다면 실행 /*
        currentavail = currentavail + alloc[k, *];
        rest = rest - {P};
      }
      else possible = false;
  }
  return (rest == null);
}
{% endhighlight %}

-------

#### Deadlock Detection
- 리소스 할당이 가능하면 항상 해줌
- `Request(Q) matrix` 이용 - Q<sub>ij</sub> : i 프로세스가 요청하는 리소스 타입 j의 개수
- 초기 모든 프로세스는 데드락된 것으로 가정(unmark)하고 데드락이 아닌 것을 하나씩 찾아내는(mark) 방식
  - 1. `자원 할당 Matrix(Allocation Matrix)`의 한 행이 모두 0인 프로세스는 데드락이 아닌 것으로 마킹 (하나도 할당되지 않았으므로 hold-and-wait이 불가능)
  - 2. `임시 벡터 W`를 `할당 가능한 자원 벡터 A(available)`로 초기화
  - 3. 현재 프로세스들 중 마킹되지 않았고, Q<sub>i</sub> 행 중 W 벡터보다 작거나 같은 i 행을 찾음
    - 3-1) 만약 그런 행이 없다면, 알고리즘을 종료함
    - 3-2) 만약 그런 행이 있다면, 프로세스를 `marking` 후 W 벡터에 해당 프로세스가 가진 자원을 더해줌`(free)`

#### Deadlock Recovery
- 데드락이 발생한 모든 프로세스를 종료시킴
  - 대부분의 OS에 적용된 방법
- 프로세스들의 체크포인트를 만들고, 데드락이 발생한 프로세스를 체크포인트로 롤백
  - 롤백과 재시작 메커니즘이 필요함
  - 다시 데드락이 발생할 수 있음
- 데드락이 없어질때까지 데드락이 발생한 프로세스들을 하나씩 종료시킴
  - 매번 데드락인지 체크해야 함
- 데드락이 없어질때까지 리소스를 하나씩 뺏음
  
----------

#### 식사하는 철학자 문제 (Dining Philosophers Problem)
- 두 철학자가 한 포크를 같이 쓸 수 없음
  - `Mutual Exclusion`
- 어느 한 철학자도 굶어 죽으면 안됨
  - `Starvation` & `Deadlock`을 피해야 함


{% highlight c %}
/* First solution to the Dining Philosophers Problem */
semaphore fork[5] = {1}; 
int i;
void philosopher(int i)
{
  while(true){
    think();
    wait(fork[i]);            /* 왼쪽 포크 */ /* 왼쪽 포크만 동시에 잡아들면 데드락이 발생함 */
    wait(fork[(i+1) mod 5]);  /* 오른쪽 포크 */ 
    eat();
    signal(fork[(i+1) mod 5]);
    signal(fork[i]);
  }
}
void main()
{
  parbegin(philosopher(0) ~ philosopher(4));
}
{% endhighlight %}

-------------

{% highlight c %}
/* Second solution to the Dining Philosophers Problem */
semaphore fork[5] = {1}; 
semaphore room = {4};
int i;
void philosopher(int i)
{
  while(true){
    think();
    wait(room);             /* room에 동시에 들어갈 수 있는 프로세스의 수는 4개로 제한 */
    wait(fork[i]);
    wait(fork[(i+1) mod 5]);
    eat();
    signal(fork[(i+1) mod 5]);
    signal(fork[i]);
    signal(room);  
  }
}
void main()
{
  parbegin(philosopher(0) ~ philosopher(4));
}
{% endhighlight %}

------------

{% highlight c %}
/* Third solution to the Dining Philosophers Problem - Monitor */
monitor dining_controller;
enum states{thinking, hungry, eating} state[5];
cond needFork[5]; /* condition variable */

void get_forks(int pid)
{
  state[pid] = hungry;
  if(state[(pid + 1) % 5] == eating || state[(pid - 1) % 5] == eating)
    cwait(needFork[pid]); /* 이웃이 먹는 중이라면 기다림 */
  state[pid] = eating; /* 이웃이 먹지 않는 중이면 먹음 */
}

void release_forks(int pid)
{
  state[pid] = thinking;
  if(state[(pid + 1) % 5] == hungry && state[(pid + 2) % 5] != eating)
    csignal(needFork[pid + 1]); /* 오른쪽 이웃이 먹을 수 있도록 함 */
  else if(state[(pid - 1) % 5] == hungry && state[(pid - 2) % 5] != eating)
    csignal(needFork[pid - 1]); /* 왼쪽 이웃이 먹을 수 있도록 함 */
}

void philosopher[k = 0 to 4]
{
  while(true){
    <think>
    get_forks(k);
    <eat>
    release_forks(k);
  }
}
{% endhighlight %}


