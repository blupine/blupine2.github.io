---
layout: post
title:  "[2021 카카오 채용연계형 인턴십]  표 편집"
subtitle: ""
date:   2021-08-20 21:33:55 +0900
categories: algorithm
tags : programmers
---

### [문제링크]({{"https://programmers.co.kr/learn/courses/30/lessons/81303"}})

## [풀이]

 1. 모든 행(노드)를 배열로 선언하고 초기화 시 next, prev 링크를 해줌
 2. 현재를 가리키는 커서를 포인터로 선언 (cursor)
 3. Up/Down의 경우 next,prev 링크를 이용해서 O(n)으로 이동
 4. Delete의 경우 cursor를 삭제하면서 deleted 플래그를 true로 갱신, 삭제된 노드들을 가지는 vector에 넣음(deleteQueue)
 5. Undo의 경우 deleteQueue의 마지막 element를 가져와서, deleted 플래그를 false로 갱신
    1. 해당 element를 복원할 때 링크 정보(prev, next)를 복원

```c++
#include <string>
#include <vector>
using namespace std;

typedef struct Node {
    int idx;
    bool deleted;
    Node *next, *prev;
}Node;

Node nodes[1000001];
Node * cursor;

void init(int n, int k) {
    for(int i = 0 ; i < n ; i++) {
        nodes[i].idx = i;
        nodes[i].deleted = false;
        nodes[i].prev = i == 0 ? NULL : &nodes[i-1];
        nodes[i].next = i == n-1 ? NULL : &nodes[i+1];
    }
    cursor = &nodes[k];
}

void up(int cnt) {
    for(int i = 0 ; i < cnt ; i++) {
        if(cursor->prev == NULL) break;
        cursor = cursor->prev;
    }
}

void down(int cnt) {
    for(int i = 0 ; i < cnt ; i++) {
        if(cursor->next == NULL) break;
        cursor = cursor->next;
    }
}

vector<Node> deleteQueue;
void remove() {
    deleteQueue.push_back(*cursor);
    cursor->deleted = true;
    if(cursor->prev != NULL) {
        cursor->prev->next = cursor->next;
    }
    if(cursor->next != NULL) {
        cursor->next->prev = cursor->prev;
    }
    
    if(cursor->next != NULL) { // 마지막 행이 아닐 경우
        cursor = cursor->next;    // 아래로 한 칸
    }else {
        cursor = cursor->prev;    // 마지막 행일 경우 이전 행으로
    }
}

void undo() {
    if(deleteQueue.size() == 0) return;
    
    Node node = deleteQueue.back();
    deleteQueue.pop_back();
    nodes[node.idx].deleted = false;
    
    Node* tmp = nodes[node.idx].prev->next;
    
    if(nodes[node.idx].prev != NULL) {
        nodes[node.idx].prev->next = &nodes[node.idx];   
    }
    if(nodes[node.idx].next != NULL) {
        nodes[node.idx].next->prev = &nodes[node.idx];
    }
}

string solution(int n, int k, vector<string> cmd) {
    string answer = "";
    init(n, k);
    
    for(string com : cmd) {
        if(com[0] == 'U') {
            up(stoi(com.substr(2)));
        }
        else if(com[0] == 'D') {
            down(stoi(com.substr(2)));
        }
        else if(com[0] == 'C') {
            remove();
        }else if(com[0] == 'Z') {
            undo();
        }
    }    
    
    for(int i = 0 ; i < n  ; i++) {
        answer += nodes[i].deleted ? "X" : "O";
    }
    return answer;
}
```