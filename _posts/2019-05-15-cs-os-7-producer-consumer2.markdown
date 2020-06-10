---
layout: post
title:  "7. Producer/Consumer problem (2) - message passing"
subtitle: "semaphore/monitor/message passing/producer-consumer"
date:   2019-05-15 19:14:07 +0900
categories: cs
tags : os
comments: true
---

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
  - `Addressing` : sender와 receiver를 어떻게 특정할 것이냐?
    - `Direct addressing` : receiver의 id를 명시해둠
      - direct addressing일 때 receiver는 다음 두 가지 방식으로 처리 가능
        - Explicit addressing
          - sender가 본인을 명시적으로 보내도록 함 designate
          - cooperating 하는 concurrent process에서 효율적인 방식
        - Implicit addressing
          - receive 할 때 sender의 id가 반환됨
    - `Indirect addressing` : 특정 receiver에게 보내는 것이 아닌 `mailbox`에 넣어둠

- **Message passing을 이용한 Mutual Exclusion**
{% highlight c %}
/* program mutualexclusion */
const int n = /* number of processes */
void P(int i)
{
    message msg;
    while(true){
        reveive(box, msg); /* blocking, 메시지가 올때까지 기다림 */
        /* critical section */
        send(box, msg);    /* nonblocking, 메시지를 넣고 진행 */
        /* remainder */
    }
}
void main()
{
    create_mailbox (box);
    send(box, null);
    parbegin(P(1), P(2), . . ., P(n));
}
{% endhighlight %} 

- **Message를 이용한 Producer-Consumer 문제**
{% highlight c %}
const int
    capacity = /* buffering capacity */;
    null = /* empty message */
int i;
void producer()
{
    message pmsg;
    while(true){
        receive(mayproduce, pmsg);
        pmsg = produce();
        send(mayproduce, pmsg);
    }
}
void consumer()
{
    message cmsg;
    while(true){
        receive(mayconsume, cmsg);
        consume(cmsg);
        send(mayproduce, null);
    }
}
void main()
{
    create_mailbox(mayproduce); /* produce 해도 좋다는 메시지 박스 */
    create_mailbox(mayconsume); /* consume 해도 좋다는 메시지 박스 */
    for(int i = 1 ; i <= capacity ; i++) send(mayproduce, null);
    parbegin(producer, consumer);
}
{% endhighlight %}