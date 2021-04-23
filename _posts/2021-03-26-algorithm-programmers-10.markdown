---
layout: post
title:  "[월간 코드 챌린지 시즌2] 모두 0으로 만들기"
subtitle: ""
date:   2021-03-26 21:22:55 +0900
categories: algorithm
tags : programmers
---

### [문제링크]({{"https://programmers.co.kr/learn/courses/30/lessons/76503"}})

## [풀이]


```java
#include <string>
#include <vector>
#include <set>
#include <queue>
#define abs(a) ((a) < (0) ? (a) * -1 : (a))
using namespace std;

long long solution(vector<int> a, vector<vector<int>> edges) {
    long long answer = 0;
    
    vector<long long> b(a.begin(), a.end());
    
    // 관계수가 적은 노드에서 큰 노드로 merge해야 함
    vector<set<int>> rel(a.size(), set<int>()); 
    for(int i = 0 ; i < edges.size() ; i++) {
        rel[edges[i][0]].insert(edges[i][1]);
        rel[edges[i][1]].insert(edges[i][0]);   
    }
    
    queue<int> q;
    for(int i = 0 ; i < rel.size(); i++) {  // 리프 노드를 찾아서 큐에 넣음
        if(rel[i].size() == 1){
            q.push(i);
        }
    }
    
    while(!q.empty()){
        int minnod = q.front();                 // 리프노드를 꺼내서
        q.pop();
        
        set<int> linked = rel[minnod];         // 해당 리프노드와 연결된 다른 노드들 번호를 가져옴
        
        long long maxnext = 0, max = 0;        
        for(set<int>::iterator iter = linked.begin(); iter != linked.end(); iter++) {
            int node = *iter;                      
            if(rel[node].size() > max){     
                max = rel[node].size();        // 해당 리프노드와 연결된 다른 노드들 중 가장 관계를 많이 가지는 노드를 고름
                maxnext = node;
            }
        }
        b[maxnext] += b[minnod];               // 해당 노드에 리프노드가 가지는 값을 merge
        rel[maxnext].erase(minnod);            // 해당 노드에서 리프노드 관계 삭제
        rel[minnod].erase(maxnext);            // 리프노드에서도 해당 노드에 대한 관계 삭제
        answer += abs(b[minnod]);              // merge한 만큼 answer에 더해줌
        
        
        if(rel[maxnext].size() == 0) {         // 더 이상 다른 노드가 없을 경우
            if(b[maxnext] != 0) answer = -1;   // 만약 남아있는 값이 0이 아닌 경우
            break;
        }
        else if(rel[maxnext].size() == 1) {    // 해당 노드도 리프노드일 경우, 큐에 추가
            q.push(maxnext);
        }
    }
    
    return answer;
}
```