---
layout: post
title:  "[2017 카카오코드 예선]보행자 천국"
subtitle: ""
date:   2021-08-23 17:37:55 +0900
categories: algorithm
tags : programmers
---

### [문제링크]({{"https://programmers.co.kr/learn/courses/30/lessons/1832"}})

## [풀이]

***다른 문제들은 안그랬는데 전역 변수에 대한 초기화 코드가 없으면 실패한다***

1. dp문제이다.
2. `dist[2][502][502]` 배열에 각 (i, j) 위치에 도달할 수 있는 경우의 수를 저장한ㄷ.
   1. `dist[0][i][j]`는 (i, j)를 왼쪽에서 진입하는 경우의 수
   2. `dist[1][i][j]`는 (i, j)를 위에서 진입하는 경우의 수

**3. 모든 거리의 합을 구하는 연산에 Mod를 취해주지 않으면 오버플로우가 발생할 수 있다.**
```c++
#include <vector>
#include <cstdio>
using namespace std;

int MOD = 20170805;
long long dist[2][502][502]; // [0][i][j] => (i, j)를 왼쪽에서 진입하는 경우의 수
// 전역 변수를 정의할 경우 함수 내에 초기화 코드를 꼭 작성해주세요.
int getval(vector<vector<int>> city, int row, int col) {
    if(row < 0 || row >= city.size() || col < 0 || col >= city.size()) return 0;
    return city[row][col];
}
int solution(int m, int n, vector<vector<int>> city_map) {
    for(int i = 0 ; i < 502 ; i++) {
        for(int j = 0 ; j < 502 ; j++) 
            dist[0][i][j] = dist[1][i][j] = 0;
    }
    
    vector<vector<int>> cpmap(m + 1, vector<int>(n + 1, 0));
    for(int i = 1 ; i <= m ; i++) {
        for(int j = 1 ; j <= n ;j++) {
            cpmap[i][j] = city_map[i - 1][j - 1];
        }
    }
    
    dist[0][1][2] = city_map[0][1] != 1 ? 1 : 0;
    dist[1][2][1] = city_map[1][0] != 1 ? 1 : 0;
    
    for(int i = 1; i <= m; i++) {
        for(int j = 1; j <= n; j++) {
            if(cpmap[i][j] == 1) continue;
            if((i == 1 && j == 2) || (i == 2 && j == 1)) continue;
            if(cpmap[i - 1][j] == 0) {
                dist[1][i][j] = (dist[0][i - 1][j] + dist[1][i - 1][j]) % MOD;
            }
            else if(cpmap[i - 1][j] == 2) {
                dist[1][i][j] = dist[1][i - 1][j] % MOD;
            }
            
            if(cpmap[i][j - 1] == 0) {
                dist[0][i][j] = (dist[0][i][j - 1] + dist[1][i][j - 1]) % MOD;
            }
            else if(cpmap[i][j - 1] == 2) {
                dist[0][i][j] = dist[0][i][j - 1] % MOD;
            }
        }
    }
    return (dist[0][m][n] + dist[1][m][n]) % MOD;
}
```