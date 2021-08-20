---
layout: post
title:  "[위클리 챌린지] 3주차"
subtitle: ""
date:   2021-08-20 21:37:55 +0900
categories: algorithm
tags : programmers
---

### [문제링크]({{"https://programmers.co.kr/learn/courses/30/lessons/84021"}})

## [풀이]

 1. `game_board`에서 빈 공간인 곳을 bfs를 이용해서 좌표계만 `vector<pair>`로 추출(get_linked_land)
    1. 추출한 좌표 pair는 정렬해서 좌상단 좌표가 가장 먼저 오게끔 해둠(후에 블록과 == 연산으로 비교를 위해)
    2. 정렬된 pair vector는 `normalize` (가장 작은 좌표를 0,0으로 평행이동) `blanks 벡터`
 2. `table`에서 블록인 좌표들을 bfs를 이용해서 마찬가지로 좌표계만 추출
    1. 추출한 좌표는 정렬해서 좌상단 좌표가 먼저 오게끔 해둠
    2. 정렬된 pair vector를 마찬가지로 normalize
    3. 90도씩 돌려보면서 `blanks 벡터`원소들과 비교

```c++
#include <string>
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;

int dir[4][2] = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};

vector<pair<int, int>> get_linked_land(vector<vector<int>> board, vector<vector<bool>> &visit, int row, int col, int tar) { 
    vector<pair<int, int>> points;
    queue<pair<int, int>> q;
    q.push(make_pair(row, col));
    visit[row][col] = true;
    while(!q.empty()) {
        pair<int, int> top = q.front();
        q.pop();
        points.push_back(top);

        for(int i = 0; i < 4; i ++) {
            int nextrow = top.first + dir[i][0];
            int nextcol = top.second + dir[i][1];
            if(nextrow >= board.size() || nextrow < 0 || nextcol >= board.size() || nextcol < 0 || board[nextrow][nextcol] != tar || visit[nextrow][nextcol] == true) 
                continue;

            visit[nextrow][nextcol] = true;
            q.push(make_pair(nextrow, nextcol));
        }
    }
    return points;
}

vector<pair<int,int>> normalize(vector<pair<int, int>> param) {
    sort(param.begin(), param.end());
    int min_first = param[0].first, min_second = param[0].second;
    for(int i = 0 ; i < param.size() ; i++) {
        param[i].first -= min_first;
        param[i].second -= min_second;
    }
    return param;
}
    

int solution(vector<vector<int>> game_board, vector<vector<int>> table) {
    int answer = 0;
    int N = table.size();

    vector<vector<bool>> visit;
    
    for(int i = 0 ; i < N ;i++) 
        visit.push_back(vector<bool>(N, false));
        

    vector<vector<pair<int,int>>> blanks;
    
    for(int i = 0 ; i < N ; i++) {
        for(int j = 0 ; j < N ; j++) {
            if(game_board[i][j] == 0 && visit[i][j] == false) {
                vector<pair<int ,int>> blank = get_linked_land(game_board, visit, i, j, 0);                
                blank = normalize(blank);
                blanks.push_back(blank);
            }
        }
    }
    
    
    for(int i = 0 ; i < N ;i++) 
        for(int j = 0 ; j < N ; j++) 
            visit[i][j] = false;

    
    for(int i = 0 ; i < N ; i++) {
        for(int j = 0 ; j < N ; j++) {
            if(table[i][j] == 1 && visit[i][j] == false) {
                vector<pair<int, int>> points = get_linked_land(table, visit, i, j, 1);             
                
                for(int i = 0 ; i < 4 ; i++) { // 90도씩 회전
                    // points 가지고 일단 맞춰보기
                    vector<pair<int,int>> tmp = points;
                    tmp = normalize(tmp);

                    bool find = false;
                    for(int j = 0 ; j < blanks.size() ; j++) {
                        if(blanks[j].size() != tmp.size()) continue;
                        if(blanks[j] == tmp) {
                            answer += blanks[j].size();
                            blanks.erase(blanks.begin() + j);
                            find = true;
                            break;
                        }
                    }
                
                    if(find) 
                        break;
                    
                    for(int i = 0 ; i < points.size() ;i++) { // rotate 90
                        int tmp = points[i].first;
                        points[i].first = points[i].second;
                        points[i].second = N - tmp - 1;
                    }

                }
            }
        }
    }    
    
    return answer;
}
```