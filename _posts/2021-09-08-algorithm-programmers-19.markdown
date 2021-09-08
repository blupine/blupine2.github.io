---
layout: post
title:  "[연습문제] 순위"
subtitle: ""
date:   2021-09-08 17:37:55 +0900
categories: algorithm
tags : programmers
---

### [문제링크]({{"https://programmers.co.kr/learn/courses/30/lessons/49191"}})

## [풀이]

노드 간의 거리를(단방향) 구해서 해결이 가능하다

1. 각 노드에서 다른 노드로 갈 수 있는 최단거리를 갱신함 (floyd)
2. 최단 거리가 갱신된 인접 행렬을 가지고 순위가 결정됐는지 판단하는 방법?
   1. 정점 i가 갈 수 있는 다른 정점들의 수 == i가 이길 수 있는 다른 노드들 -- (a)
   2. 정점 i로 갈 수 있는 다른 정점들의 수 == i가 이길 수 없는 다른 노드들 -- (b)
   3. `(a) + (b) == (n - 1)` // 자기 자신을 제외하고 개수가 맞아야 함


```c++
#include <string>
#include <vector>
#define INF 987654321
#define MIN(a, b) ((a) < (b) ? (a) : (b))
using namespace std;

int solution(int n, vector<vector<int>> results) {
    int answer = 0;
    
    vector<vector<int>> adj(n + 1, vector<int>(n + 1, INF));
    
    for(int i = 0 ; i < results.size() ; i++) {
        adj[results[i][0]][results[i][1]] = 1;
    }
    
    // floyd
    for(int k = 1 ; k <= n; k++) {
        // node k를 거쳐갔을 때 최단거리 갱신
        for(int i = 1; i <= n ; i++) {
            for(int j = 1; j <= n ; j++) {
                adj[i][j] = MIN(adj[i][j], adj[i][k] + adj[k][j]);
            }
        }
    }
    
    
    for(int i = 1; i <= n ; i++) {
        // 정점 i가 갈 수 있는 다른 노드의 수 = toIJ
        // 정점 i로 갈 수 있는 다른 노드들의 수 = toJI
        int toIJ = 0, toJI = 0;
        for(int j = 1; j <= n ; j++) {
            if(adj[i][j] != INF) toIJ++;
            if(adj[j][i] != INF) toIJ++;
        }
        if(toIJ + toJI == (n - 1))
            answer++;
    }
    return answer;
}
```