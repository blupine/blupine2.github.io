---
layout: post
title:  "[2021 KAKAO BLIND RECRUITMENT] 카드 짝 맞추기"
subtitle: ""
date:   2021-03-05 14:25:23 +0900
categories: algorithm
tags : programmers
---
### [문제링크]({{"https://programmers.co.kr/learn/courses/30/lessons/72415"}})

## [풀이]


숫자 쌍을 선택해서 해당 쌍으로 먼저 이동, 삭제 -> dfs 반복

숫자 쌍은 Permutation 해서 모든 조합을 다 dfs 해봐야 함 (1먼저 없애고, 2 먼저 없애는 것이 결과가 다를 수 있음)

{% highlight c++ %}

#include <string>
#include <vector>
#include <deque>
#include <set>
#include <utility>
#include <algorithm>

#define MIN(a, b) ((a) > (b) ? (b) : (a))
#define INBOUND(r, c) (!((r) > 3 || (r) < 0 || (c) > 3 || (c) < 0))
using namespace std;

const int dir[4][2] = { {-1, 0}, {1, 0}, {0, -1}, {0, 1} };
struct Pos {
    int r; int c; 
    Pos(int r, int c):r(r),c(c) {}
};

int _min = 0x7fffffff;
vector<vector<Pos>> positions(7);
vector<int> perm;
vector<vector<int>> gboard;

Pos get_next_position(int d, int r, int c, int ctrl_pressed) {
    if(ctrl_pressed) {
        while(true) {
            r += dir[d][0];
            c += dir[d][1];
            
            if(!INBOUND(r, c)){
                return Pos(r - dir[d][0], c - dir[d][1]);
            }
                        
            if(gboard[r][c] != 0) {
                return Pos(r, c);
            }
        }
        
    }else {
        int nr = r + dir[d][0];
        int nc = c + dir[d][1];
        if(!INBOUND(nr, nc)) 
            return Pos(r, c);
        
        return Pos(nr, nc);
    }
}

int get_distance(int r1, int c1, int r2, int c2) {
    deque<Pos> q;
    q.push_back(Pos(r1, c1));
    int distance = 0;
    while(!q.empty()) {
        
        deque<Pos> nextq;
        
        while(!q.empty()){
            Pos pos = q.front(); q.pop_front();
            if(pos.r == r2 && pos.c == c2) {
                return distance;
            }

            // 키 조작 : 방향키, 컨트롤+방향키, 엔터
            for(int d = 0 ; d < 8 ; d++) {
                Pos next = get_next_position(d / 2, pos.r, pos.c, d % 2); // 같은 방향으로, ctrl을 눌렀을때와 안눌렀을 때 2번 호출
                if(next.r != pos.r || next.c != pos.c){
                    for(Pos it : nextq) {
                        if(it.r == next.r && it.c == next.c) continue; // 이미 큐에 넣은 것일 경우
                    }
                    nextq.push_back(next);
                }
            }            
        }
        q = nextq;
        distance++;
    }
    return distance;
} 

void dfs(int cur_r, int cur_c, int depth, int score) {
    if(perm.size() == depth) {
        _min = MIN(_min, score);   
        return;
    }
    if(score > _min) return;
    
    int dist1 = 0, dist2 = 0;
    
    Pos first = positions[perm[depth]][0]; 
    Pos second = positions[perm[depth]][1];
    int card = gboard[first.r][first.c];
    
    dist1 += get_distance(cur_r, cur_c, first.r, first.c) + 1; // + 1 for "Enter"
    dist1 += get_distance(first.r, first.c, second.r, second.c) + 1;
    gboard[first.r][first.c] = gboard[second.r][second.c] = 0;
    dfs(second.r, second.c, depth + 1, score + dist1);
    gboard[first.r][first.c] = gboard[second.r][second.c] = card;
    
    dist2 += get_distance(cur_r, cur_c, second.r, second.c) + 1;
    dist2 += get_distance(second.r, second.c, first.r, first.c) + 1;
    gboard[first.r][first.c] = gboard[second.r][second.c] = 0;
    dfs(first.r, first.c, depth + 1, score + dist2);
    gboard[first.r][first.c] = gboard[second.r][second.c] = card;
}

int solution(vector<vector<int>> board, int r, int c) {
    gboard = board;
    
    set<int> p;
    for(int i = 0 ; i < 4 ; i++) {
        for(int j = 0 ; j < 4 ; j++) {
            if(board[i][j] != 0) {
                positions[board[i][j]].push_back(Pos(i, j));
                p.insert(board[i][j]);
            }
        }
    }

    for(auto it : p) perm.push_back(it);
    
    do {
        // arr[0] ~ arr[n-1] 순서대로 뒤집음
        dfs(r, c, 0, 0);
    }while(next_permutation(perm.begin(), perm.end()));
    
    return _min;
}
{% endhighlight %}