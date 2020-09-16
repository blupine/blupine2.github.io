---
layout: post
title:  "2105. [모의 SW 역량테스트] 디저트 카페"
subtitle: ""
date:   2019-07-11 16:12:55 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV5VwAr6APYDFAWu"}})


## [풀이]

{% highlight c %}
#include <iostream>
#include <vector>
#include <utility>
#include <algorithm>
#include <string.h>
using namespace std;
#define DEBUG 0
#define MAXMAP 22
int total = 0;
typedef pair<int, int> pos;
enum {NE, SW, SE, NW};
int dir[4][2] = { {-1, 1}, {1, -1}, {1, 1}, {-1, -1} };
int map[MAXMAP][MAXMAP];
int mapsize;
void get_input(){
	memset(map, -1, sizeof(map));
	cin>>mapsize;
	for(int i = 1 ; i <= mapsize ; i++)
		for(int j = 1 ; j <= mapsize ; j++)
			cin >> map[i][j];
}
int dfs(vector<pos> moving, int dirchange, int cookie[101]){
	int top = 0;
	int sol[4] = {0,};
	if(dirchange == 0){
		pos curr = moving.back();
		pos nextSE = pos(curr.first + dir[SE][0], curr.second + dir[SE][1]);
		if( map[nextSE.first][nextSE.second] != -1 && cookie[map[nextSE.first][nextSE.second]] == 0){
			vector<pos> newmoving = moving;
			int newcookie[101];
			memcpy(newcookie, cookie, sizeof(int)*101);
			newcookie[map[nextSE.first][nextSE.second]] = 1;
			newmoving.push_back(nextSE);

			int res = dfs(newmoving, 0, newcookie);
			if(res != 0)
				sol[top++] = res;
		}

		pos nextSW = pos(curr.first + dir[SW][0], curr.second + dir[SW][1]);
		if( moving.size() != 1 && map[nextSW.first][nextSW.second] != -1 && cookie[map[nextSW.first][nextSW.second]] == 0){
			vector<pos> newmoving = moving;
			newmoving.push_back(nextSW);
			int newcookie[101];
			memcpy(newcookie, cookie, sizeof(int)*101);
			newcookie[map[nextSW.first][nextSW.second]] = 1;
			int res = dfs(newmoving, 1, newcookie);
			if(res != 0)
				sol[top++] = res;
		}
	}

	if(dirchange == 1){
		pos curr = moving.back();
		pos next = pos(curr.first + dir[SW][0], curr.second + dir[SW][1]);
		if(map[next.first][next.second] != -1 && cookie[map[next.first][next.second]] == 0){			
			vector<pos> newmoving = moving;
			newmoving.push_back(next);
			int newcookie[101];
			memcpy(newcookie, cookie, sizeof(int)*101);
			newcookie[map[next.first][next.second]] = 1;
			int res = dfs(newmoving, 1, newcookie);
			if(res != 0)
				sol[top++] = res;
		}
		int depth = curr.first - moving.at(0).first;
		int SWdepth = (moving.at(0).second + depth - curr.second) / 2;
		int SEdepth = depth - SWdepth;
		
		for(int i = 1 ; i <= SEdepth; i++){
			pos NWpos = pos(curr.first + dir[NW][0]*i, curr.second + dir[NW][1]*i);

			if(map[NWpos.first][NWpos.second] == -1 || cookie[map[NWpos.first][NWpos.second]] == 1){ 
				return max(max(sol[0], sol[1]), max(sol[2], sol[3]));
			}
			else
				cookie[map[NWpos.first][NWpos.second]] = 1;
		}
		
		curr = pos(curr.first + dir[NW][0] * SEdepth, curr.second + dir[NW][1] * SEdepth);
		for(int i = 1 ; i <= SWdepth; i++){
			if(i == SWdepth) {
				sol[top++] = (SEdepth + SWdepth) * 2;
				break;
			}
			pos NEpos = pos(curr.first + dir[NE][0]*i, curr.second + dir[NE][1]*i);
			if(( map[NEpos.first][NEpos.second] == -1 || cookie[map[NEpos.first][NEpos.second]] == 1 )){
				return max(max(sol[0], sol[1]), max(sol[2], sol[3]));
			}
			else
				cookie[map[NEpos.first][NEpos.second]] = 1;
		}
		
	}

	return max(max(sol[0], sol[1]), max(sol[2], sol[3]));
}
int available_dir(pos position){
	int ret = 0;
	for(int i = 0 ; i < 4; i++)
		if(map[position.first + dir[i][0]][position.second + dir[i][1]] != -1)
			ret++;
	return ret;
}
int solve(){
	vector<int> sol;

	for(int i = 1 ; i <= mapsize; i++)
		for(int j = 1; j <= mapsize; j++){
			if(available_dir(pos(i, j)) < 2) continue;
			vector<pos> position;
			int cookie[101] = {0,};
			position.push_back(pos(i, j));
			cookie[map[i][j]] = 1;
			int d = dfs(position, 0, cookie);
			if(d != 0)
				sol.push_back(d);
		}
	sort(sol.begin(), sol.end());
	if(sol.size() == 0)
		return -1;
	else
		return sol.back();
}
int main(){
	int ntest;
	int sol[50];
	cin >> ntest;
	for(int i = 0 ; i < ntest ; i++){
		get_input();
		sol[i] = solve();
	}
	for(int i = 0 ; i < ntest; i++)
		cout << "#" << i+1 << " " << sol[i] << endl;
	return 0;
}
{% endhighlight %}