---
layout: post
title:  "9. Unix Concurrency Mechanism - signal"
subtitle: "signal/signal handler/"
date:   2019-05-25 21:55:07 +0900
categories: cs
tags : os
comments: true
---

### Unix Concurrency Mechanism
- Pipes
  - FIFO queue, 한 프로세스가 쓰고, 다른 프로세스가 읽음
  - 실제 구현은 circular buffer, producer-consumer 모델
  - ex) `ls | more`, `ps | sort` etc
- Messages
  - 유닉스는 메시지 패싱을 지원하기 위해 `msgsnd`, `msgrcv` 시스템콜을 지원함
  - 메시지는 바이트들의 `block`

- Signal
  - 비동기적 이벤트의 발생을 프로세스에게 전달해주기 위한 소프트웨어적 메커니즘 (하드웨어 인테럽트와 유사)
  - **Sending**
    - 커널이 대상 프로세스의 `context`에 특정 상태`state`를 업데이트 함 `sending signal`
    - 커널이 시그널을 보내는 이유
      - divided-by-zero`(SIGFPE)` 또는 자식프로세스의 종료`(SIGCHLD)` 같은 시스템 이벤트를 탐지할 경우
      - 다른 프로세스가 `kill` 시스템 콜을 통해 대상 프로세스에게 명시적으로 시그널을 요청할 경우
  - **Receiving**
    - 커널이 시그널을 보내면 수신하게 됨
    - 두가지 수신 방법
      - Default action(igonre, terminate, terminate & dump`(coredump)`
      - `signal handler`라는 `user-level` 함수를 통해 시그널을 처리
    - 비동기 인테럽트가 발생했을 때 호출되는 hardware exception handler와 유사함
    - 
  - ![1]({{"assets/img/cs/os/9/1.png" | absolute_url}})
  - 시그널 상태
    - `pending`
      - 시그널이 보내졌지만 아직 수신은 되지 않았을 때의 상태
      - 특정 시그널은 최대 하나까지만 `pending`될 수 있음 `(not queueing)`, 같은 타입이 들어오면 discard
    - `block`
      - 프로세스는 특정 시그널을 `block`할 수 있음
      - 보낼수는 있지만 `unblocked`이 될 때 까지 수신되지는 않음
    - 커널은 각 프로세스마다 `pending`, `blocked` 상태를 관리하기 위해 프로세스의 `context`에 `bit vectors`를 둠
      - `pending`
        - 커널은 K 시그널을 보낼 때 K번째 비트를 1로 설정함
        - 커널은 K 시그널이 수신되면 K번째 비트를 0으로 설정함
      - `blocked`
        - `user-level`에서 `sigprocmask` 함수 호출을 통해 설정할 수 있음

#### Receiving Signals
- 커널이 예외 처리를 끝마치고 프로세스에게 다시 컨트롤을 줄 때
  - 커널이 `pnb(pending and unblocked)`를 계산함
    - `pnb = pending & ~blocked` : pending 상태이고 non-block 상태인 시그널
    - 만약 `pnb`가 0일 경우?
      - 처리할 시그널이 없음, 그냥 프로세스의 다음 명령어를 실행하면 됨
    - `pnb`가 0이 아닐 경우
      - 0이 아닌 k 번째 비트를 처리하기 위해 프로세스가 k 시그널을 수신하도록 함
      - 0이 아닌 모든 비트가 처리된 다음에 프로세스의 다음 명령어 실행

#### Installing Signal Handling - user-level signal handler
- `signal` 함수 : default 시그널 액션을 등록할 수 있음
  - `handler_t *signal (int signum, handler_t *handler)`
-  handler
   -  `SIG_IGN` : 기존 핸들러를 실행하지 않고 시그널을 무시함
   -  `SIG_DFL` : 시그널에 대해 default 핸들러를 실행시키도록 함
   -  또는 사용자 정의 함수에 대한 주소 `(signal handler)`
   - 용어
     - `installing` : 시그널에 대한 default 핸들러를 변경하는 것
     - `catching` : 시그널 핸들러를 ***호출***하는 것
     - `handling` : 시그널 핸들러를 ***실행***하는 것

{% highlight c %}
#include "csapp.h"

void handler(int sig){
    static int beeps = 0;
    printf("BEEP\n");
    if(++beeps < 5) Alarm(1); /* 1초 뒤에 SIGALRM 전달 됨 */
    else{
        printf("BOOM!\n");
        exit(0);
    }
}
int main(){
    Signal(SIGALRM, handler); /* install SIGALRM handler */
    Alarm(1);   /* 1초 뒤에 SIGALRM 시그널 전달 */
    while(1){   /* 각 시그널 핸들러들이 루프로 들어와서 종료됨 */
        exit(0);
    }

}
{% endhighlight %}