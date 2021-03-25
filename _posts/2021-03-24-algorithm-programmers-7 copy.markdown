---
layout: post
title:  "[2019 카카오 개발자 겨울 인턴십] 불량 사용자"
subtitle: ""
date:   2021-03-25 23:33:55 +0900
categories: algorithm
tags : programmers
---

### [문제링크]({{"https://programmers.co.kr/learn/courses/30/lessons/64064"}})

## [풀이]

1. 불량 사용자를 순서대로 모든 사용자에 맞춰보면서 dfs 수행

2. 불량 사용자의 '\*' 문자에 해당하는 위치를 비교 대상 사용자도 '\*'로 마스킹 해서 비교

3. dfs를 이용했기 때문에 불량 사용자 조합은 중복으로 나올 수 있음 (ex) ["frodo", "fradi"] == ["fradi", "frodo"]
    - 중복 조합을 관리하기 위해, 찾은 사용자 조합을 정수형으로 만들어서 set으로 관리
    - ex) 1, 2, 3번째 사용자가 차단 목록 조합으로 만들어지면 key = 123으로 set에 추가

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.HashSet;
class Solution {
    String[] g_banned_id, g_user_id;
    boolean[] visited;
    HashSet<Integer> solutions;
    
    public int solution(String[] user_id, String[] banned_id) {
        g_user_id = user_id;
        g_banned_id = banned_id;
        visited = new boolean[user_id.length];
        
        solutions = new HashSet<Integer>(); // 정답 조합을 관리할 Set
        dfs(0);
        return solutions.size();
    }
    
    void dfs(int depth) {
        if(depth >= g_banned_id.length) {
            int key = 0;
            for(int i = 0 ; i < visited.length; i++) {
                if(visited[i])
                    key = key * 10 + i;  // bann된 아이디를 조합해서 key로 만듬(같은 id 조합은 같은 key가 만들어짐)
            }
            solutions.add(key);
            return;
        }

        String target = g_banned_id[depth];
        
        for(int i = 0; i < g_user_id.length; i++) {
            if(!visited[i] && target.length() == g_user_id[i].length()) { 
                // 이전에 bann되지 않고, id 길이가 같을 경우
                
                String cmp = g_user_id[i];
                char[] chcmp = cmp.toCharArray();
                
                for(int j = 0; j < target.length(); j++) {
                    if(target.charAt(j) == '*')  
                        chcmp[j] = '*';         // '*'인 위치를 비교 대상 유저에 마스킹
                }

                cmp = new String(chcmp);
                if(cmp.equals(target)) {        // 마스킹된 문자열이 불량 사용자 id와 일치할 경우
                    visited[i] = true;          // 해당 id를 bann 처리하고 다음 dfs로 넘어감
                    dfs(depth + 1);
                    visited[i] = false;
                }
            }
        }
        
    }
}
```