---
layout: post
title:  "5. Competing Processes"
subtitle: ""
date:   2019-04-30 11:21:27 +0900
categories: cs
tags : os
comments: true
---

### Competing Processes
- Sharing a global resources? 다음 문제가 발생 가능
  - Need for mutual exclusion
  - Deadlock
  - Starvation
- Mutual Exclusion?
  - 상호 배제
  - 한번에 한 프로세스만 리소스를 사용하도록 제한하는 것
  - Critical Section을 한 프로세스만 진입하도록 보장해야 함
  - 데커알고리즘
  - 간단하게 처리하는 방법?
    - 임계 영역에 들어갈 때 interrupt를 disable, 나올 때 enable
      - 프로세서가 한 개일 때만 가능
      - 멀티 프로세서일 때는 왜 안되냐?
        - 다른 프로세스가 다른 프로세서에 dispatch 되어서 critical section에 들어갈 수 있음
  - Key terminolody
    -  Atomic operation
       - 여려 개의 명령어를 하나의 실행 단위로 묶음 (나눌 수 없음)
       - All or nothing
       - HW-level atomic operations
         - test-and-set, fetch-and-add, compare-and-swap, load-link/store-conditional
       - SW-level solutions
         - 크리티컬 섹션에서 명령어 실행
       - 결과는 success / faileure
     - Critical Section
       - 공유 자원에 접근하는 프로세스의 코드 영역으로 반드시 한 프로세스만 해당 자원에 접근할 수 있도록 보장되어야 하는 영역
     - Mutual Exclusion
       - 어떤 한 프로세스가 공유 자원에 접근하는 크리티컬 섹션에 있을 때, 필요로 하는 자원을 다른 프로세스가 점유(크리티컬 섹션)하지 못해야 함
     - Race condition
       - 공유 자원에 대해 여러 프로세스가 read/write를 할 때 실행 결과가 프로세스들 간의 interleaving에 따라 달라짐
  
- **그러면 실제로 critical section을 어떻게 구현하냐?**
  - Special machine instructions (하드웨어적으로 지원)
    - 두 개의 명령어를 하나로 처리함 (reading and writing on single memory location)
    - Test-and-set, fetch-and-add, compare-and-swap etc
      - Test-and-set이 대부분 일반적임
        - x86, IA64, SPARC, IBM z serices etc.
    - 멀티코어 환경에서도 사용될 수 있음
    - 문제점?
      - busy waiting
        - 자원이 release될 때까지 test-and-set 반복해야 함
      - deadlock and stravation 가능
    - 장점?
      - 프로세스, 프로세서의 개수와 상관 없이 크리티컬 섹션 구현 가능
      - 


- compare-and-swap ? 
  - 특정 메모리의 값을 테스트하고, 참일 경우 다른 값으로 변경

  {% highlight c %}
    /* compare-and-swap 예시 */
    while(ture){
      while(compare_and_swap(&bolt, 0, 1) == 1){
        /* bold == 0을 참조한 프로세서가 1로 바꾸고 해당 루프를 빠져나옴 */
        /* do nothing */
      }
      /* critical section */
      bolt = 0;
      /* remainder */
    }
  {% endhighlight %}
    
- exchange instruction
  - 메모리의 값을 자신이 전달한 레지스터의 값으로 변경함
  {% highlight c %}
    /* exchange 예시 */
    void exchange(int * register, int * memory)
    {
      int temp;
      temp = *memory;
      *memory = *register;
      *register = temp;
    }
    
    ...

    int keyi = 1;
    while(true){
      do exchange(&keyi, &bolt)
      while(keyi != 0);
      /* critical section */
      bolt = 0;
      /* remainder */
    }

  {% endhighlight %}

-------------

### Semaphore
- 소프트웨어적인 메커니즘
- 공유 자원 접근을 컨트롤 하는 변수 (공유 가능한 자원의 개수로 초기화됨)
- 두 가지 operation
  - V operation (also known as "signal")
    - increment the semaphore (release)
  - P operation (also known as "wait")
    - decrement the semaphore (allocation)
- 두 가지 종류
  - binary semaphore
    - 0 또는 1의 값을 가짐 (locked / unlocked)
  - counting semaphore
    - resource count (자원의 개수)
- strong semaphore
  - 가장 오래 block된 프로세스를 큐에서 꺼냄
- weak semaphore
  - 순서 없이 아무나 wake up 시킬 수 있는 것
{% highlight c %}
/* counting semaphore example */
struct semaphore{
  int count;
  queueType queue;
};
void semWait(semaphore s) /* P operation */
{
  s.count--;
  if(s.count < 0){
    /* 자원이 없을 때 대기열로 들어감 */
    /* place this process in s.queue */
    /* block this process */
  }
}
void semSignal(semaphore s) /* V operation */
{
  s.count++;
  if(s.count <= 0){
    /* 자원을 release할 때 대기중인 프로세스가 있을 경우 */
    /* remove a process P from s.queue */
    /* place process P on ready list */
  }
}
{% endhighlight %}

-----------

{% highlight c %}
/* critical section with semaphore example */
const int n = /* number of processes */;
semaphore s = 1;
void P(int i)
{
  while(true){
    semWait(s);
    /* critical section */
    semSignal(s);
    /* remainder */
  }
}

{% endhighlight %}

#### Producer / Consumer problem ?
- 하나 이상의 Producer가 데이터를 생성하고 버퍼에 넣음
- 하나의 Consumer가 버퍼로부터 데이터를 가져와서 소비함 
- **한번에 하나의 Producer 또는 Consumer만 버퍼에 접근이 가능함 (race condition이 가능하기 때문에)**
- 문제점?
  - Producer가 이미 다 찬 버퍼에 데이터를 삽입하지 않도록 보장해야 함
  - Consumer는 빈 버퍼로부터 데이터를 가져오는 요청을 하지 않도록 보장해야 함
  - **semaphore를 이용하여 이런 상황에서의 synchronization 또한 가능함**


{% highlight c %}
/* producer-consumer 문제 - 오답 코드 */
int n;
binary_semaphore s = 1, delay = 0;
void producer()
{
    while(true){
        producer();
        semWaitB(s);
        append();
        n++;
        if(n == 1) semSignalB(delay); /* producer가 데이터를 생산하고 delay set */
        semSignalB(s);
    }
}
void consumer()
{
    semWaitB(delay); /* producer가 데이터를 생산하고 시그널을 보내면 진행 */
    while(true){
        semWaitB(s);
        take();
        n--;
        semSignalB(s);
        consume();
        if(n == 0) {
            /* 더이상 데이터가 없으면 기다림 */
            /* 이 코드로 진입하기 전에 producer가 실행되면 아래 시나리오처럼 over consuming 문제가 발생할 수 있음*/
            semWaitB(delay);
        }
    }
}
void main()
{
    n = 0;
    parbegin(producer, consumer); /* producer, consumer 동시 실행 */
}
{% endhighlight %}

### 가능한 시나리오 
![1]({{"assets/img/cs/os/5/1.png" | absolute_url}})

{% highlight c %}
/* producer-consumer 문제 - 오답 코드2 */
void consumer()
{
    semWaitB(delay); /* producer가 데이터를 생산하고 시그널을 보내면 진행 */
    while(true){
        semWaitB(s);
        take();
        n--;
        if(n == 0) semWaitB(delay); /* 이렇게 크리티컬 섹션 안에 넣는다면? */
        /* 크리티컬 섹션에서 시그널을 기다리기 때문에 데드락이 발생함 */
        semSignalB(s);
        consume();
    }
}
{% endhighlight %}

----------
{% highlight c %}
/* producer-consumer 문제 - 정답코드 */
int n;
binary_semaphore s = 1, delay = 0;
void producer()
{
    while(true){
        producer();
        semWaitB(s);
        append();
        n++;
        if(n == 1) semSignalB(delay); /* producer가 데이터를 생산하고 delay set */
        semSignalB(s);
    }
}
void consumer()
{
    int m; /* 크리티컬 섹션에서 빠져나오기 전에 아이템의 수를 기억하기 위한 로컬 변수 */
    semWaitB(delay); /* producer가 데이터를 생산하고 시그널을 보내면 진행 */
    while(true){
        semWaitB(s);
        take();
        n--;
        m = n;
        semSignalB(s);
        consume();
        if(m == 0) semWaitB(delay);
        /* 이 코드가 실행되기 전에 context switching되어 n이 변하더라도 기다리게 되어 이전의 문제를 해결할 수 있음 */
        
    }
}
{% endhighlight %}

---------

{% highlight c %}
/* counting semaphore - 정답코드 2 */
semaphore n = 0, s = 1;
void producer()
{
    while(true){
        produce();
        semWait(s);
        append();
        semSignal(s);
        semSignal(n);    
    }
}
void consumer()
{
    while(true){
        semWait(n);
        semWait(s);
        take();
        semSignal(s);
        consume();
    }
}
{% endhighlight %}

-------------

{% highlight c %}
/* counting semaphore - 정답코드 3 */
semaphore n = 0, s = 1;
void producer()
{
    while(true){
        produce();
        semWait(s);
        append();
        semSignal(n);  /* n과 s의 위치를 바꾸면? - 문제 없음 - 서로  */
        semSignal(s);
    }
}
void consumer()
{
    while(true){
        semWait(n);
        semWait(s);
        take();
        semSignal(s);
        consume();
    }
}
{% endhighlight %}

-----------

{% highlight c %}
/* counting semaphore - 오답코드 1 */
semaphore n = 0, s = 1;
void producer()
{
    while(true){
        produce();
        semWait(s);
        append();
        semSignal(s);
        semSignal(n);
    }
}
void consumer()
{
    while(true){
        semWait(s);
        semWait(n);  /* n과 s의 위치를 바꾸면? - consumer가 먼저 실행되어서 데드락 가능  */
        take();
        semSignal(s);
        consume();
    }
}
{% endhighlight %}

#### Semaphore를 내부적으로 어떻게 구현하냐?
- 하드웨어적으로 구현하는 것은 불가능
- 소프트웨어적으로 데커, 피터슨 알고리즘 있음
- atomic instruction으로도 구현 가능하지만 busy waiting의 한계를 벗어나지 못함