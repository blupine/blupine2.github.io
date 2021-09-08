---
layout: post
title:  "[연습문제] 줄 서는 방법"
subtitle: ""
date:   2021-09-06 17:37:55 +0900
categories: algorithm
tags : programmers
---

### [문제링크]({{"https://programmers.co.kr/learn/courses/30/lessons/12936#"}})

## [풀이]



```c++
#include <string>
#include <vector>
#include <algorithm>
#include <queue>
using namespace std;

long long npac(int n) {
    long long res = 1;
    while(n) res *= n--;
    return res;
}

vector<int> solution(int n, long long k) {
    vector<int> answer;
    k -= 1; // 시작을 0번째로 고정
    priority_queue<int> pq, tmp;
    
    for(int i = 1 ; i <= n ;i++) 
        pq.push(i * -1);
    
    while(k) {
        int mock = k / npac(n - 1);
        // 남은 수 중 mock + 1번째 숫자를 넣음 (mock은 0부터 시작하므로 + 1)
        
        for(int i = 0 ; i < mock ;i++) {
            tmp.push(pq.top());
            pq.pop();
        }
        
        //printf("k : %d, mock : %d, %d\n", k, mock, pq.top());
        answer.push_back(pq.top() * -1);
        pq.pop();
        
        while(!tmp.empty()) {
            pq.push(tmp.top());
            tmp.pop();
        }
        
        k = k % npac(n - 1);
        n--;
    }
    
    while(!pq.empty()) {
        answer.push_back(pq.top() * -1);
        pq.pop();
    }
    return answer;
}
```