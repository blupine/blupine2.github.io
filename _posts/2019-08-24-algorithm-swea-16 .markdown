---
layout: post
title:  "5650. [모의 SW 역량테스트] 핀볼 게임"
subtitle: ""
date:   2019-08-24 14:25:13 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXRF8s6ezEDFAUo"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <deque>
#include <cstring>
#include <cstdio>
#include <algorithm>
#include <utility>
#define OPOSITE(i)	(i % 2 == 0 ? i + 1 : i - 1)
using namespace std;
#define MAXMAP 102
#define DEBUG 0
int direction[4][2] = { {-1,0}, {1, 0}, {0, -1}, {0, 1} };
int N;
int map[MAXMAP][MAXMAP];
int wormhole[5][2][2];
class Pinball{
public:
	int irow, icol;
	int row, col;
	int dir;
	int score;
	Pinball(int r, int c, int d){
		irow = r; icol = c;
		row = r; col = c; dir = d; score = 0;
	}
};
int move_pinball(Pinball ball){
	while(1){
		int newrow = ball.row + direction[ball.dir][0];
		int newcol = ball.col + direction[ball.dir][1];
		if(DEBUG){
			printf("[+] DEBUG  init_pos [ %d, %d ], moving [%d, %d]\n", ball.irow, ball.icol, newrow, newcol);
		}
		if((map[newrow][newcol] == -1) || 
			(newrow == ball.irow && newcol == ball.icol)){
			return ball.score;
		}
		if(map[newrow][newcol] == 0){
			ball.row = newrow;
			ball.col = newcol;
			continue;
		}

		if(map[newrow][newcol] == 5 
			|| (ball.dir == 0 && (map[newrow][newcol] == 1 || map[newrow][newcol] == 4 ))
			|| (ball.dir == 1 && (map[newrow][newcol] == 2 || map[newrow][newcol] == 3 ))
			|| (ball.dir == 2 && (map[newrow][newcol] == 3 || map[newrow][newcol] == 4 ))
			|| (ball.dir == 3 && (map[newrow][newcol] == 1 || map[newrow][newcol] == 2 ))){
				ball.dir = OPOSITE(ball.dir);
				ball.score++;
		}
		else if(map[newrow][newcol] == 1){
			if(ball.dir == 1){
				ball.dir = 3;
			}
			else if(ball.dir == 2){
				ball.dir = 0;
			}
			ball.score++;
		}
		else if(map[newrow][newcol] == 2){
			if(ball.dir == 0){
				ball.dir = 3;
			}
			else if(ball.dir == 2){
				ball.dir = 1;
			}
			ball.score++;
		}
		else if(map[newrow][newcol] == 3){
			if(ball.dir == 3){
				ball.dir = 1;
			}
			else if(ball.dir == 0){
				ball.dir = 2;
			}
			ball.score++;
		}
		else if(map[newrow][newcol] == 4){
			if(ball.dir == 1){
				ball.dir = 2;
			}
			else if(ball.dir == 3){
				ball.dir = 0;
			}
			ball.score++;
		}
		else if(map[newrow][newcol] > 5){
			int hole_index = map[newrow][newcol] - 6;
			int i = (wormhole[hole_index][0][0] == newrow && wormhole[hole_index][0][1] == newcol ? 1 : 0);
			newrow = wormhole[hole_index][i][0];
			newcol = wormhole[hole_index][i][1];
		}
		ball.row = newrow;
		ball.col = newcol;
	}	
}

void get_input(){
	memset(wormhole, 0, sizeof(wormhole));
	for(int i = 0 ; i < MAXMAP ; i++)
		for(int j = 0 ; j < MAXMAP ; j++)
			map[i][j] = 5;

	cin >> N;
	for(int i = 1 ; i <= N ; i++){
		for(int j = 1 ; j <= N ; j++){
			cin >> map[i][j]; 
			if(map[i][j] > 5){
				int hole_index = map[i][j] - 6;
				int k = (wormhole[hole_index][0][0] == 0 ? 0 : 1);
				wormhole[hole_index][k][0] = i;
				wormhole[hole_index][k][1] = j;
			}
		}
	}
}
int solve(){
	int max = 0;
	for(int i = 1; i <= N ; i++){
		for(int j = 1 ; j <= N; j++){
			if(map[i][j] == 0){
				int res = 0;
				res = move_pinball(Pinball(i, j, 0));
				if(res > max) max = res;
				res = move_pinball(Pinball(i, j, 1));
				if(res > max) max = res;
				res = move_pinball(Pinball(i, j, 2));
				if(res > max) max = res;
				res = move_pinball(Pinball(i, j, 3));
				if(res > max) max = res;
			}
		}
	}
	return max;
}
int main(){
	int sol[50] = {0,};
	int ntest;
	cin >> ntest;
	for(int i = 0 ; i < ntest ; i++){
		get_input();
		sol[i] = solve();
	}
	for(int i = 0 ; i < ntest ; i++)
		cout << "#" << i+1 << " " << sol[i] << endl;
}
{% endhighlight %}