---
layout: post
title:  "[2017 카카오코드 예선]카카오프렌즈 컬러링북"
subtitle: ""
date:   2021-03-18 22:05:16 +0900
categories: algorithm
tags : programmers
---

### [문제링크]({{"https://programmers.co.kr/learn/courses/30/lessons/1829"}})

## [풀이]

각 좌표가 아직 counting된 적이 없으면(!visited) area 카운트를 증가시키고 해당 좌표에서 bfs를 시작, 최대 넓이를 구함



{% highlight java %}

import java.util.Queue;
import java.util.LinkedList;
class Solution {    
    public static int[][] dir = { {0, 1}, {1, 0}, {-1, 0}, {0, -1} };
    public int[][] visited, gpicture;
    public int max_size, area, gm, gn;

    public int[] solution(int m, int n, int[][] picture) {
        gm = m;
        gn = n;
        gpicture = picture;
        visited = new int[m][n];
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n ; j++) {
                if(picture[i][j] == 0)
                    visited[i][j] = 1;
            }
        }        
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n ; j++) {
                if(visited[i][j] == 0){
                    area++;
                    int num = bfs(new Pos(i, j));
                    max_size = Math.max(num, max_size);
                }
            }
        }
        
        
        int[] answer = new int[2];
        answer[0] = area;
        answer[1] = max_size;
        return answer;
    }
    
    public int bfs(Pos pos) {
        int num = 0;
        Queue<Pos> q = new LinkedList<Pos>();
        q.offer(pos);
        visited[pos.row][pos.col] = 1;
        while(q.size() != 0) {
            num++;
            Pos cur = q.poll();
            for(int i = 0 ; i < 4 ; i++) {
                Pos next = new Pos(cur.row + dir[i][0], cur.col + dir[i][1]);
                if(inBound(next) && visited[next.row][next.col] == 0 
                   && gpicture[cur.row][cur.col] == gpicture[next.row][next.col]) {
                    visited[next.row][next.col] = 1;
                    q.offer(next);
                }
            }
        }
        return num;
    }
    
    public boolean inBound(Pos pos) {
        if(pos.row < gm && pos.row >= 0 && pos.col < gn && pos.col >= 0)
            return true;
        return false;
    }

    class Pos{
        int row, col;
        Pos(int r, int c) {
            row = r; col = c;
        }
    }
}
{% endhighlight %}