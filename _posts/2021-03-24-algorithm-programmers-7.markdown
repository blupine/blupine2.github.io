---
layout: post
title:  "[2017 카카오코드 본선] 리틀 프렌즈 사천성"
subtitle: ""
date:   2021-03-20 21:33:55 +0900
categories: algorithm
tags : programmers
---

### [문제링크]({{"https://programmers.co.kr/learn/courses/30/lessons/1836"}})

## [풀이]

1. 각 알파벳의 좌표를 알파벳을 Key로 하는 HashMap으로 만든다. (r1, c1, r2, c2)

2. HashMap의 키를 정렬하여 알파벳 순으로 만들고, 정렬된 알파벳 순으로 제거가 가능한지 확인한다(dfs)

3. dfs를 계속 하면서 알파벳이 모두 완성될 경우 정답처리 -> 빠져나옴 (애초에 알파벳 순으로 찾은 것이기 때문에 조합 중 가장 빠른 순서가 보장됨)


근데 58, 59번 라인에 dfs 빠녀나오고 원복해주는 코드를 주석처리 안하면 실패하는데, 로직 상 필요가 없긴 하지만 있어도 상관은 없어보이는데.. 왜 안되는지 몰겠다.             

```java
visited[i] = false;
cBoard[a.r1][a.c1] = cBoard[a.r2][a.c2] = org;
```

{% highlight java %}
import java.util.HashMap;
import java.util.Arrays;

class Solution {
    boolean find = false;
    char[][] cBoard;
    boolean[] visited;
    Object[] mapkey;
    String answer = "IMPOSSIBLE";
    HashMap<Character, Alphabet> hmap = new HashMap<Character, Alphabet>();
    
    public String solution(int m, int n, String[] board) {
        cBoard = new char[m][n];
        
        for(int i = 0 ; i < m ; i++) {
            for(int j = 0 ; j < n; j++) {
                cBoard[i][j] = board[i].charAt(j);
                if('A' <= cBoard[i][j] && cBoard[i][j] <= 'Z') {
                    // 해당 알파벳 찾아서, 좌표 추가
                    char key = cBoard[i][j];
                    
                    if(hmap.containsKey(key)) { // 이미 이전에 나온 알파벳일 경우 -> 두 번째 좌표로 추가
                        hmap.get(key).r2 = i;
                        hmap.get(key).c2 = j;
                    }else {
                        Alphabet a = new Alphabet(key, i, j); // 처음 나온 알파벳일 경우 -> 첫 번째 좌표로 추가
                        hmap.put(key, a);
                    }
                }
            }
        }
        
        // 알파벳 정렬 후 빠른 순서 부터 삭제 시도
        mapkey = hmap.keySet().toArray();       
        Arrays.sort(mapkey);
        visited = new boolean[mapkey.length];
        dfs("");
        
        return answer;
    }
    
    void dfs(String str) {
        if(str.length() == mapkey.length){
            answer = str;
            find = true;
            return;
        }
            
        // 알파벳 순서대로 삭제 가능한 것 먼저 삭제 -> 알파벳 순서 상 가장 빠른 거 하나만 찾을 수 있음
        for(int i = 0 ; i < visited.length ; i++) {
            if(!visited[i] && !find) {
                Alphabet a = hmap.get(mapkey[i]);
                if(isPossible(a.r1, a.c1, a.r2, a.c2)){
                    char org = cBoard[a.r1][a.c1];
                    cBoard[a.r1][a.c1] = cBoard[a.r2][a.c2] = '.';
                    visited[i] = true;
                    str += mapkey[i];
                    dfs(str);
                    // visited[i] = false;
                    // cBoard[a.r1][a.c1] = cBoard[a.r2][a.c2] = org;
                }
            }
        }
    }
    
    // 입력 상, r1은 r2보다 무조건 작거나 같음
    boolean isPossible(int r1, int c1, int r2, int c2) {
        
        char org = cBoard[r1][c1];
        cBoard[r1][c1] = cBoard[r2][c2] = '.';
        
        for(int r = r1; r <= r2; r++) {
            // 첫 번째 좌표 기준으로 수직 아래 방향으로 내려가는게 가능한지 확인
            if(cBoard[r][c1] != '.') break;
            if(r == r2) {                
                int lc = c1 < c2 ? c1 : c2;
                int rc = c1 < c2 ? c2 : c1;
                for(int c = lc; c <= rc; c++){
                    if(cBoard[r2][c] != '.') break;
                    if(c == rc) {
                        cBoard[r1][c1] = cBoard[r2][c2] = org;
                        return true;
                    }
                }
            }
        }
        
        for(int r = r1; r <= r2; r++) {
            // 첫 번째 좌표 기준으로 수직 아래, 또는 위 방향으로 가능한지 확인
            if(cBoard[r][c2] != '.') break;
            if(r == r2) {
                int lc = c1 < c2 ? c1 : c2;
                int rc = c1 < c2 ? c2 : c1;

                for(int c = lc; c <= rc; c++){
                    if(cBoard[r1][c] != '.') break;
                    if(c == rc) {
                        cBoard[r1][c1] = cBoard[r2][c2] = org;
                        return true;
                    }
                }
            }
        }
        
        cBoard[r1][c1] = cBoard[r2][c2] = org;
        return false;
    }

    class Alphabet implements Comparable<Alphabet> {
        char ch;
        int r1, c1, r2, c2;
        
        Alphabet(char ch, int r, int c) {
            ch = ch;
            r1 = r;
            c1 = c;
        }
        
        @Override
        public int compareTo(Alphabet target) {
            return ch - target.ch;
        }
    }
}
{% endhighlight %}