---
layout: post
title:  "[2020 KAKAO BLIND RECRUITMENT] 블록 이동하기"
subtitle: ""
date:   2021-04-06 23:33:55 +0900
categories: algorithm
tags : programmers
---

### [문제링크]({{"https://programmers.co.kr/learn/courses/30/lessons/60063"}})

## [풀이]

1. 로봇의 상태(가로, 세로 방향)에 따라 회전할 수 있는 좌표를 하드코딩해서 풀이..

2. bfs로..


```java
import java.util.Queue;
import java.util.LinkedList;
class Solution {
    int[][] dir = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
    boolean[][][] visit;
    int[][] gBoard;
    int N;
    public int solution(int[][] board) {
        int answer = 0;
        N = board.length;
        gBoard = board;
        visit = new boolean[2][N][N];  // 로봇이 가로 상태, 세로 상태일 때의 visit 배열
        visit[0][0][0] = true;
        
        Queue<Robot> queue = new LinkedList<>();
        Robot r = new Robot(0, 0, 0, 1, 0, 0);
        queue.add(r);
        
        while(!queue.isEmpty()) {
            Robot robot = queue.poll();
            int row1 = robot.row1, col1 = robot.col1, row2 = robot.row2, col2 = robot.col2, status = robot.status;
            if((row1 == N - 1 && col1 == N - 1) || (row2 == N - 1 && col2 == N -1)){
                return robot.move;
            }           
            
            // 로봇의 상태에 따라서 회전할 수 있는 경우의수를 다 회전시켜봄, visit안돼있으면 visit처리 하고 queue에 넣음
            
            // 좌우, 상하로 움직이는 경우
            for(int i = 0 ; i < 4 ; i++) {
                int nr1 = row1 + dir[i][0], nr2 = row2 + dir[i][0];
                int nc1 = col1 + dir[i][1], nc2 = col2 + dir[i][1];
                if(isValid(nr1, nc1) && isValid(nr2, nc2) && !visit[status][nr1][nc1]) {
                    visit[status][nr1][nc1] = true;
                    Robot rb = new Robot(nr1, nc1, nr2, nc2, status, robot.move + 1);
                    queue.add(rb);    
                 }
            }
            
            if(robot.status == 0) { // 로봇이 가로방향일 경우
                // (row1, col1) -> (row1-1, col1) -> (row1-1, col1+1)
                // (row1, col1) -> (row1+1, col1) -> (row1+1, col1+1)
                
                // (row2, col2) -> (row2-1, col2) -> (row2-1, col2-1)
                // (row2, col2) -> (row2+1, col2) -> (row2+1, col2-1)           
            
                if(isValid(row1 - 1, col1) && isValid(row1 - 1, col1 + 1) && !visit[1][row1 - 1][col1 + 1]){
                    visit[1][row1 - 1][col1 + 1] = true;
                    Robot rb = new Robot(row1 - 1, col1 + 1, row2, col2, 1, robot.move + 1);
                    queue.add(rb);
                }     
                if(isValid(row1 + 1, col1) && isValid(row1 + 1, col1 + 1) && !visit[1][row2][col2]){
                    visit[1][row2][col2] = true;
                    Robot rb = new Robot(row2, col2, row1 + 1, col1 + 1, 1, robot.move + 1);
                    queue.add(rb);
                }     
                if(isValid(row2 - 1, col2) && isValid(row2 - 1, col2 - 1) && !visit[1][row2 - 1][col2 - 1]){
                    visit[1][row2 - 1][col2 - 1] = true;
                    Robot rb = new Robot(row2 - 1, col2 - 1, row1, col1, 1, robot.move + 1);
                    queue.add(rb);
                }     
                if(isValid(row2 + 1, col2) && isValid(row2 + 1, col2 - 1) && !visit[1][row1][col1]){
                    visit[1][row1][col1] = true;
                    Robot rb = new Robot(row1, col1, row2 + 1, col2 - 1, 1, robot.move + 1);
                    queue.add(rb);
                }     
            }
            else { // 로봇이 세로방향일 경우                
                // (row1, col1) -> (row1, col1+1) -> (row1+1, col1+1)
                // (row1, col1) -> (row1, col1-1) -> (row1+1, col1-1)
                
                // (row2, col2) -> (row2, col2+1) -> (row2-1, col2+1)
                // (row2, col2) -> (row2, col2-1) -> (row2-1, col2-1)
                if(isValid(row1, col1 + 1) && isValid(row1 + 1, col1 + 1) && !visit[0][row2][col2]){
                    visit[0][row2][col2] = true;
                    Robot rb = new Robot(row2, col2, row1 + 1, col1 + 1, 0, robot.move + 1);
                    queue.add(rb);
                }     
                if(isValid(row1, col1 - 1) && isValid(row1 + 1, col1 - 1) && !visit[0][row1 + 1][col1 - 1]){
                    visit[0][row1 + 1][col1 - 1] = true;
                    Robot rb = new Robot(row1 + 1, col1 - 1, row2, col2, 0, robot.move + 1);
                    queue.add(rb);
                }     
                if(isValid(row2, col2 + 1) && isValid(row2 - 1, col2 + 1) && !visit[0][row1][col1]){
                    visit[0][row1][col1] = true;
                    Robot rb = new Robot(row1, col1, row2 - 1, col2 + 1, 0, robot.move + 1);
                    queue.add(rb);
                }     
                if(isValid(row2, col2 - 1) && isValid(row2 - 1, col2 - 1) && !visit[0][row2 - 1][col2 - 1]){
                    visit[0][row2 - 1][col2 - 1] = true;
                    Robot rb = new Robot(row2 - 1, col2 - 1, row1, col1, 0, robot.move + 1);
                    queue.add(rb);
                }     
            }   
        }  
        return answer;
    }
    
    boolean isValid(int r, int c) {
        if(0 <= r && r < N && 0 <= c && c < N && gBoard[r][c] == 0)
            return true;
        return false;
    }
    
    class Robot {
        int row1, col1, row2, col2;
        int move;
        int status; // (status == 0) : 가로, (status == 1) : 세로
        Robot(int r1, int c1, int r2, int c2, int status, int move) {
            this.row1 = r1;
            this.col1 = c1;
            this.row2 = r2;
            this.col2 = c2;
            this.status = status;
            this.move = move;
        } 
    }
}
```