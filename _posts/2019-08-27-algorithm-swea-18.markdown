---
layout: post
title:  "5656. [모의 SW 역량테스트] 벽돌 깨기"
subtitle: ""
date:   2019-08-27 17:23:13 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXRQm6qfL0DFAUo"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <deque>
#include <cstring>
#include <algorithm>
#include <utility>
using namespace std;
#define MAXWIDTH 14
#define MAXHEIGHT 17
typedef pair<int, int> pos;
int dir[4][2] = { {-1, 0}, {1, 0}, {0, -1}, {0, 1} };
int map[MAXHEIGHT][MAXWIDTH];
int N, W, H;
void get_input(){
	memset(map, -1, sizeof(map));
	cin >> N >> W >> H;
	for(int i = 1; i <= H ; i++)
		for(int j = 1 ; j <= W ; j++)
			cin >> map[i][j];
}
int break_block(int col, int cpmap[MAXHEIGHT][MAXWIDTH]){
	int flag[MAXHEIGHT][MAXWIDTH];
	memset(flag, 0, sizeof(flag));
	deque<pos> zeros;
	deque<pos> q;
	for(int i = 1 ; i <= H ; i++){
		if(cpmap[i][col] > 0){
			q.push_back(pos(i, col));
			break;
		}
	}
	while(!q.empty()){
		pos curr = q[0];
		q.pop_front();
		zeros.push_back(curr);
		flag[curr.first][curr.second] = 1;
		for(int i = 0 ; i < 4 ; i++){
			for(int j = 1 ; j < cpmap[curr.first][curr.second] ; j++){
				int newrow = curr.first  + dir[i][0] * j;
				int newcol = curr.second + dir[i][1] * j;
				if(flag[newrow][newcol] == 0 && (newrow >= 1 && newrow <= H) && (newcol >= 1 && newcol <= W))
					q.push_back(pos(newrow, newcol));	
			}
		}
	}
	for(int i = 0 ; i < zeros.size(); i++)
		cpmap[zeros[i].first][zeros[i].second] = 0;
	
	for(int i = 1 ; i <= W ; i++){
		int zero_row = 0;
		for(int j = H ; j >= 1 ; j--){
			if(cpmap[j][i] == 0){
				int index = 0;
				zero_row = j;
				for(int k = j - 1 ; k >= 1 ; k--){
					if(cpmap[k][i] != 0){
						index = k;
						break;
					}
				}
				if(index != 0){
					for(int k = index ; k >= 1 ; k--){
						cpmap[zero_row - (index - k)][i] = cpmap[k][i];
						cpmap[k][i] = 0;
					}
				}
			}
		}	
	}		
}

int dfs(int depth, int bnum[4]){
	if(depth == N){
		int cpmap[MAXHEIGHT][MAXWIDTH];
		memcpy(cpmap, map, sizeof(cpmap));
		for(int i = 0 ; i < N ; i++)
			break_block(bnum[i], cpmap);
		int ret = 0;
		for(int i = 1 ; i <= H ; i++)
			for(int j = 1 ; j <= W ; j++)
				if(cpmap[i][j] > 0)
					ret++;
		return ret;
	}
	else{
		deque<int> sol;
		for(int i = 1 ; i <= W ;i++){
			bnum[depth] = i;
			int s = dfs(depth + 1, bnum);
			if(s == 0) return 0;
			sol.push_back(s);
		}
		sort(sol.begin(), sol.end());
		return sol[0];
	}
}
int solve(){
	deque<int> sol;
	int bnum[4];
	return dfs(0, bnum);
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