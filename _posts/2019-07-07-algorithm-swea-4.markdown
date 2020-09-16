---
layout: post
title:  "1953. [모의 SW 역량테스트] 탈주범 검거"
subtitle: ""
date:   2019-07-03 14:46:39 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV5PpLlKAQ4DFAUq"}})


## [풀이]

{%highlight c%}
#include <iostream>
#include <vector>
#include <utility>
#include <algorithm>
#define DEBUG 0
#define EAST  0
#define WEST  1
#define SOUTH 2
#define NORTH 3
#define MAXMAP 51
using namespace std;
typedef pair<int, int> pos;
int map[MAXMAP][MAXMAP] = {-1,};
int rowsize, colsize, holerow, holecol, time_consumed;
int dir[4][2] = { {0, 1}, {0, -1}, {1, 0}, {-1, 0} }; // 동서남북
void get_input(){
	//clearing map
	for(int i = 0 ; i < MAXMAP ; i++)
		for(int j = 0 ; j < MAXMAP ; j++)
			map[i][j] = -1;

	cin >> rowsize >> colsize >> holerow >> holecol >> time_consumed;
	
	for(int i = 0 ; i < rowsize; i++){
		for(int j = 0 ; j < colsize; j++){
			cin >> map[i][j];
		}
	}
}
int cango(int direction, pos curr){
	pos next = make_pair(curr.first + dir[direction][0], curr.second + dir[direction][1]);
	if(map[next.first][next.second] == 0)
		return 0;
	if(next.first < 0 || next.first >= rowsize || next.second < 0 || next.second >= colsize)
		return 0;
	if(direction == EAST){ // if 동 
		if(map[curr.first][curr.second] == 1 || map[curr.first][curr.second] == 3
			|| map[curr.first][curr.second] == 4 || map[curr.first][curr.second] == 5){
			if(map[next.first][next.second] == 1 || map[next.first][next.second] == 3
				|| map[next.first][next.second] == 6 || map[next.first][next.second] == 7)
				return 1;
		}
	}
	else if(direction == WEST){ // 서
		if(map[curr.first][curr.second] == 1 || map[curr.first][curr.second] == 3
			|| map[curr.first][curr.second] == 6 || map[curr.first][curr.second] == 7){
			if(map[next.first][next.second] == 1 || map[next.first][next.second] == 3
				|| map[next.first][next.second] == 4 || map[next.first][next.second] == 5)
				return 1;
		}
	}
	else if(direction == SOUTH){ // 남 
		if(map[curr.first][curr.second] == 1 || map[curr.first][curr.second] == 2 
			|| map[curr.first][curr.second] == 5 || map[curr.first][curr.second] == 6){
			if(map[next.first][next.second] == 1 || map[next.first][next.second] == 2
				|| map[next.first][next.second] == 4 || map[next.first][next.second] == 7)
			return 1;
		}
	}
	else if(direction == NORTH){ // 북
		if(map[curr.first][curr.second] == 1 || map[curr.first][curr.second] == 2 
			|| map[curr.first][curr.second] == 4 || map[curr.first][curr.second] == 7){
			if(map[next.first][next.second] == 1 || map[next.first][next.second] == 2
				|| map[next.first][next.second] == 5 || map[next.first][next.second] == 6)
			return 1;
		}
	}
	return 0;
};
vector<pos> dfs(int from, int depth, pos next) {
	vector<pos> ret;
	if(depth > time_consumed)
		return ret;
	ret.push_back(next);
	if(from != EAST && cango(EAST, next)){
		vector<pos> temp;
		pos newpos = make_pair(next.first + dir[EAST][0], next.second + dir[EAST][1]);
		temp = dfs(WEST, depth + 1, newpos);
		if(temp.size() != 0){
			ret.reserve(ret.size() + temp.size());
			ret.insert(ret.end(), temp.begin(), temp.end());
		}
	}
	if(from != WEST && cango(WEST, next)){
		vector<pos> temp;
		pos newpos = make_pair(next.first + dir[WEST][0], next.second + dir[WEST][1]);
		temp = dfs(EAST, depth + 1, newpos);
		if(temp.size() != 0){
			ret.reserve(ret.size() + temp.size());
			ret.insert(ret.end(), temp.begin(), temp.end());
		}
	}
	if(from != SOUTH && cango(SOUTH, next)){
		vector<pos> temp;
		pos newpos = make_pair(next.first + dir[SOUTH][0], next.second + dir[SOUTH][1]);
		temp = dfs(NORTH, depth + 1, newpos);
		if(temp.size() != 0){
			ret.reserve(ret.size() + temp.size());
			ret.insert(ret.end(), temp.begin(), temp.end());
		}
	}
	if(from != NORTH && cango(NORTH, next)){
		vector<pos> temp;
		pos newpos = make_pair(next.first + dir[NORTH][0], next.second + dir[NORTH][1]);
		temp = dfs(SOUTH, depth + 1, newpos);
		if(temp.size() != 0){
			ret.reserve(ret.size() + temp.size());
			ret.insert(ret.end(), temp.begin(), temp.end());
		}
	}
	return ret;
}

int solve(){
	vector<pos> result = dfs(-1, 1, make_pair(holerow, holecol));
	sort(result.begin(), result.end());
	result.erase(unique(result.begin(), result.end()), result.end());
	return result.size();
}
int main(){
	int sol[50];
	int ntest;
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