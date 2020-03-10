---
layout: post
title:  "6. Producer/Consumer problem"
subtitle: "semaphore/monitor/message passing/producer-consumer"
date:   2019-05-10 17:53:14 +0900
categories: cs
tags : os
comments: true
---

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


----------------------

#### Monitor?
- Motivation
  - Semaphore를 이용하여 크리티컬 섹션을 보호하기가 쉽지 않음 
  - `semWait`, `semSignal` 함수들이 여기저기 흩어져 있으면 무튼 어려움..
- Semaphore와 동일한 기능을 제공하지만 컨트롤이 쉬운 자료형
- 프로세스 간의 Mutual exclusion, Synchronization을 보다 쉽게, 직관적으로 만듬
- 여러 개의 프로시져, 초기화 코드, 로컬 데이터로 구성됨
  - 로컬 데이터는 Monitor의 프로시져만 접근 가능함
  - **프로세스가 모니터에 진입하기 위해선 모니터의 프로시져를 호출해야 함**
  - Condition variable
    - `cwait(c)`    : 호출하는 프로세스를 `condition "c"` 에서 대기하도록 함
    - `csignal(c)`  : `condition "c"`에서 블락된 프로세스의 실행을 재개함

{% highlight c %}
/* monitor 영역 시작 */
monitor boundedbuffer;
char buffer[N];                 /* space for N items */
int nextin;                     /* producer가 생성할 인덱스 */
int nextout;                    /* consumer가 소비할 인덱스 */
int count;                      /* number of items in buffer */
condition notfull, notempty;    /* condition variable for synchronization */

void append(char x)
{
    if(count == N) cwait(notfull); /* buffer가 다 찼을 땐 notfull까지 대기 */
    buffer[nextin] = x;
    nextin = (nextin + 1) % N;
    count++;
    /* one more item in buffer */
    csignal(notempty); /* notempty 상태를 기다리는 다른 프로세스가 실행될 수 있도록 시그널 */
}
void take(char x)
{
    if(count == 0) cwait(notempty); /* buffer가 비어있으면 notempty까지 기다림 */
    x = buffer[nextout];
    nextout = (nextout + 1) % N;
    count--;
    csignal(notfull); /* notfull 상태를 기다리는 다른 프로세스가 실행될 수 있도록 */
}
{
    /* monitor body - buffer initially empty */
    nextin = nextout = count = 0;
}
/* monitor 영역 끝 */
---
/* monitor 밖에서 monitor 내부의 프로시져(append, take)를 호출 */
void producer()
{
    char x;
    while(true){
        produce(x);
        append(x);
    }
}
void consumer()
{
    char x;
    while(ture){ 
        take(x);
        consume(x);
    }
}

{% endhighlight %}


----------------------

#### Message passing
- 가장 일반적이고 쉬운 방법
- Mutual exclusion, Synchroniztion 뿐만 아닌 Communication 또한 가능
- 실제 동작?
  - `send(des, msg)`
    - 프로세스가 `des` 프로세스에게 `msg`를 보냄 
  - `receive(src, msg)`
    - 프로세스가 `src`로부터 `msg`를 받길 기다림
  - `sender`와 `receiver`는 blocking이 될수도, nonblocking이 될수도 있음
    - blocking send / blocking receive (rendezvous - 둘 다 기다림)
    - nonblocking send, blocking receive (가장 일반적임)
    - nonblocking send, nonblocking reveive