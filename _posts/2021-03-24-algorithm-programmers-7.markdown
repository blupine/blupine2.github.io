---
layout: post
title:  "[2020 KAKAO BLIND RECRUITMENT] 외벽 점검"
subtitle: ""
date:   2021-03-24 23:33:55 +0900
categories: algorithm
tags : programmers
---

### [문제링크]({{"https://programmers.co.kr/learn/courses/30/lessons/60062"}})

## [풀이]

1. 각 취약 저짐(weak)이 어느 곳에서 시작해도 탐색이 가능하게끔 shift된 배열을 만듬(weaks 배열)
    - ex) n == 12, weak[] = [1, 5, 6, 10] -> [5, 6, 10, 13], [6, 10, 13, 17] ... etc

2. 각 취약 지점의 시작 지점부터, 가장 많이 이동할 수 있는 친구부터 배치해보는 방법으로 dfs 시작

```java
import java.util.Arrays;
class Solution {
    int _min = 0x7fffffff;
    int[] disc;         // 가장 많이 이동할 수 있는 친구부터 탐색해보기 위해 dist 배열을 오름차순으로 저장할 배열
    int[][] weaks;      // 취약 지점을 모든 조합이 가능하게 (ex. n == 12, [1, 5, 6, 10]  -> [5, 6, 10, 13] ... etc)
    boolean[] visited;  // 친구들 조합을 dfs로 해보기 위해
    
    public int solution(int n, int[] weak, int[] dist) {
        int answer = 0;
        weaks = new int[weak.length][weak.length];
        for(int i = 0; i < weak.length; i++) {
            for(int j = 0; j < weak.length; j++) {
                weaks[i][j] = weak[(i + j) % weak.length];
                weaks[i][j] += ((i + j) / weak.length) != 0 ? n : 0;
            }
        }
        
        
        Arrays.sort(dist);
        disc = new int[dist.length];
        for(int i = 0; i < dist.length; i++) { // 내림차순으로, 가장 큰 dist 먼저 검사하기 위해 역정렬
            disc[i] = dist[dist.length - i - 1];
        }
        
        
        for(int i = 0; i < weaks.length; i++) {
            visited = new boolean[disc.length];
            dfs(weaks[i], 0, 0);
        }
         
        if(_min > disc.length)
            _min = -1;
       
        return _min;
    }
    
    
    public void dfs(int[] weak, int widx, int used) { 
        if(used > _min) return;     // 가지치기
        if(widx >= weak.length) {
            _min = Math.min(used, _min);
            return;
        }
        
        for(int i = 0; i < visited.length; i++){
            if(!visited[i]) {
                visited[i] = true;
                int target = weak[widx] + disc[i]; // 현재 친구를 사용했을 때, 어디까지 점검이 가능한지
                int next;
                for(next = widx; next < weak.length; next++) {
                    if(weak[next] > target) break; // 현재 친구를 사용했을 때, 점검 가능한 지점을 초과했을 경우 break
                }
                dfs(weak, next, used + 1);         // 해당 초과 지점부터 다음 dfs 호출
                visited[i] = false;
            }
        }
       
    }
}
```