---
layout: post
title:  "[2021 카카오 채용연계형 인턴십] 거리두기 확인하기"
subtitle: ""
date:   2021-08-21 16:37:55 +0900
categories: algorithm
tags : programmers
---

### [문제링크]({{"https://programmers.co.kr/learn/courses/30/lessons/81302"}})

## [풀이]
1. 각 방의 i, j를 순회하면 서 P(학생이 있는 위치)가 발견되면 해당 자리에서 bfs로 맨해튼 거리 탐색(bfs)

```c++
#include <string>
#include <vector>
#include <queue>
#define ABS(a) ((a) < 0 ? (a) * -1 : (a))

using namespace std;

const int dir[4][2] = { {0, 1}, {1, 0}, {-1, 0}, {0, -1} };

bool isok(vector<string> place, int row, int col) {
    // row, col 위치에서 맨해튼 거리 2 내에 P가 있는지 검사
    vector<vector<bool>> visit(place.size(), vector<bool>(place.size(), false));
    queue<pair<int, int>> q;
    q.push(make_pair(row, col));
    visit[row][col] = true;
    while(!q.empty()) {
        pair<int, int> top = q.front();
        q.pop();
        
        for(int i = 0 ; i < 4 ; i++) {
            int nextrow = top.first + dir[i][0];
            int nextcol = top.second + dir[i][1];
            
            if(nextrow < 0 || nextcol < 0 || nextrow >= place.size() || nextcol >= place.size() ||
                visit[nextrow][nextcol] == true || ABS(nextrow - row) + ABS(nextcol - col) > 2 ||
                place[nextrow][nextcol] == 'X')
                continue;
            
            if(place[nextrow][nextcol] == 'P') 
                return false;
            
            visit[nextrow][nextcol] = true;
            q.push(make_pair(nextrow, nextcol));
        }   
    }
    return true;
}

vector<int> solution(vector<vector<string>> places) {
    vector<int> answer;
    for(vector<string> place : places) {        
        bool ok = true;
        for(int i = 0 ; i < place.size(); i++) {
            for(int j = 0 ; j < place[i].size() ; j++) {
                if(place[i][j] == 'P') {
                    if(!isok(place, i, j)) {
                        ok = false;  
                        j = i = place.size();
                        break;
                    }
                }
            }
        }
        answer.push_back(ok ? 1 : 0);
    }
    return answer;
}
```