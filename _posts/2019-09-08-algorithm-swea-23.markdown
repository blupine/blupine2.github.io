---
layout: post
title:  "1226. [S/W 문제해결 기본] 7일차 - 미로1"
subtitle: ""
date:   2019-09-08 17:59:08 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV14vXUqAGMCFAYD"}})

## [풀이]

{% highlight c %}
#include <stdio.h>

#define MAXN 16
#define MAXQ (MAXN*MAXN*4)

typedef struct q_struct{
    int x;
    int y;
} q_struct;

const int DIR[4][2] = { {1,0},{-1,0},{0,1},{0,-1} };

int visit[MAXN+2][MAXN+2];
int map[MAXN+2][MAXN+2];
q_struct queue[MAXQ];
int q_h, q_t;

void push(int x, int y){
    queue[q_t].x = x;
    queue[q_t].y = y;
    q_t = (q_t + 1)%MAXQ;
}

q_struct* pop(q_struct** node){
    if(q_t == q_h)
        return NULL;
    *node = &queue[q_h];
    q_h = (q_h + 1)%MAXQ;
    return &queue[0];
}

void init(){
    q_h = q_t = 0;
}

int main(){
    for(int t=1; t<11; t++){
        init();
        scanf("%*d");
        for(int n_1=1; n_1<MAXN+1; n_1++){
            for(int n_2=1; n_2<MAXN+1; n_2++){
                scanf("%1d", &map[n_1][n_2]);
                visit[n_1][n_2] = 0;
            }
        }
        int result =0;
        q_struct* temp;
        push(2,2);
        while(pop(&temp)){
            if(map[temp->x][temp->y] == 1)
                continue;
            if(map[temp->x][temp->y] == 3){
                result = 1;
                break;
            }
            if(visit[temp->x][temp->y] == 1)
                continue;
            visit[temp->x][temp->y] = 1;
            for(int i=0; i<4; i++)
                push(temp->x + DIR[i][0], temp->y + DIR[i][1]);
        }
        printf("#%d %d\n",t, result);
    }
}
{% endhighlight %}