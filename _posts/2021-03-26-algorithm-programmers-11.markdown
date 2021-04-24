---
layout: post
title:  "[월간 코드 챌린지 시즌1] 스타 수열"
subtitle: ""
date:   2021-03-26 21:33:55 +0900
categories: algorithm
tags : programmers
---

### [문제링크]({{"https://programmers.co.kr/learn/courses/30/lessons/70130"}})

## [풀이]


```c++
#include <string>
#include <vector>
#include <stdlib.h>
#define max(a, b) ((a) < (b) ? (b) : (a))
using namespace std;

vector<int> cnt(500001, 0); // cnt[x] : x의 가능한 수 카운트
vector<int> pos(500001, -1); // pos[x] : x의 직전 인덱스

int solution(vector<int> a) {
    for(int i = 0 ; i < a.size() ; i++) {
        int val = a[i];
        if(pos[val] + 1 < i) { // 왼쪽
            cnt[val]++;
            pos[val] = i;            
        }
        else if(i + 1 < a.size() && val != a[i + 1] ){ // 오른쪽
            cnt[val]++;
            pos[val] = i + 1;
        }else {
            pos[val] = i;
        }
    }
    
    int answer = 0;
    for(int i = 0 ; i < a.size() ; i++) {
        answer = max(cnt[a[i]], answer);
    }    
    
    return answer * 2;
}
```