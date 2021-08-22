---
layout: post
title:  "[Summer/Winter Coding(~2018)] 기지국 설치"
subtitle: ""
date:   2021-08-22 17:37:55 +0900
categories: algorithm
tags : programmers
---

### [문제링크]({{"https://programmers.co.kr/learn/courses/30/lessons/12979"}})

## [풀이]

min_cov : 0~n까지 빈틈 없이 커버가 된 영역 중 최대 n

1. 각 기지국의 위치를 확인
   1. 해당 기지국이 커버할 수 있는 min, max 값을 구함
   2. 현재 까지 구한 min_cov 보다 min이 클 경우 추가 설치해야 할 기지국 개수 산출
2. 마지막까지 기지국을 설치해도 min_cov는 n보다 작을 수 있음(끝까지 커버가 안됨)
   1. 그런 경우 또 필요한 기지국 개수 산출해서 더해줌

```c++
#include <iostream>
#include <vector>
using namespace std;

int solution(int n, vector<int> stations, int w)
{
    int answer = 0;
    int min_cov = 0, cov = 2 * w + 1;
    for(int station : stations) {
        int min = station - w;
        int max = station + w;
        if(min_cov < min) {
            // [min_cov] ~ [min] 사이를 커버해야 함
            answer += (min - min_cov - 1) / cov + ((min - min_cov - 1) % cov != 0 ? 1 : 0);
        }
 
        min_cov = max;
    }
    
    if(min_cov < n) {
        answer += (n - min_cov) / cov + ((n - min_cov) % cov != 0 ? 1 : 0);
    }

    return answer;
}
```