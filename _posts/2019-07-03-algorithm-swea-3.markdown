---
layout: post
title:  "1949. [모의 SW 역량테스트] 등산로 조성"
subtitle: ""
date:   2019-07-03 14:46:39 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV5PoOKKAPIDFAUq"}})


## [풀이]

{% highlight c %}
#include <iostream>
#include <vector>
#include <utility>
#include <string.h>
#include <algorithm>
#define MAXMAP 10
#define DEBUG 0
using namespace std;

int global_map[MAXMAP][MAXMAP] = {-1,};
int dir[4][2] = { {0,1},{0,-1},{1,0},{-1,0} };
int mapsize;
int work;
vector<pair<int, int> > maxvect;

void clear_map(){
	for(int i = 0 ; i < MAXMAP; i++)
		for(int j = 0 ; j < MAXMAP ; j++)
			global_map[i][j] = -1;
}
void get_input(){
	clear_map();
	int max = 0;
	for(int i = 1 ; i <= mapsize ; i++){
		for(int j = 1 ; j <= mapsize ; j++){
			cin >> global_map[i][j];
			if(global_map[i][j] > max){
				max = global_map[i][j];
				maxvect.clear();
				maxvect.push_back(make_pair(i, j));
			}
			else if(global_map[i][j] == max)
				maxvect.push_back(make_pair(i, j));
		}
	}
	if(DEBUG){
		for(int i = 0 ; i < maxvect.size(); i++){
			cout << maxvect.at(i).first << " " << maxvect.at(i).second << endl;
		}
	}
}
int dfs(int depth, int worknum, pair<int, int> next, int map[MAXMAP][MAXMAP]){
	vector<int> ret;
	
	for(int i = 0 ; i < 4 ; i++){ // 동, 서, 남, 북
		int newrow = next.first + dir[i][0];
		int newcol = next.second + dir[i][1];
		if(map[newrow][newcol] != -1){
			if(map[newrow][newcol] < map[next.first][next.second]){
				int newmap[MAXMAP][MAXMAP];
				memcpy(newmap, map, (sizeof(int) * MAXMAP * MAXMAP));
				newmap[next.first][next.second] = -1;
				int res = dfs(depth+1, worknum, make_pair(newrow, newcol), newmap);
				ret.push_back(res);
			}
			else if((map[newrow][newcol] - map[next.first][next.second] + 1) <= worknum){
				int newmap[MAXMAP][MAXMAP];
				int workcount = map[newrow][newcol] - map[next.first][next.second] + 1;
				memcpy(newmap, map, (sizeof(int) * MAXMAP * MAXMAP));
				newmap[newrow][newcol] -= workcount;
				newmap[next.first][next.second] = -1;
				int res = dfs(depth + 1, 0, make_pair(newrow, newcol), newmap);
				ret.push_back(res);
			}
		}
	}
	
	if(ret.size() == 0)
		return depth;
	else{
		sort(ret.begin(), ret.end());
		return ret.back();
	}
}
int solve(){
	vector<int> sols;
	for(int i = 0 ; i < maxvect.size(); i++)
		sols.push_back(dfs(1, work, maxvect.at(i), global_map));

	sort(sols.begin(), sols.end());
	return sols.back();
}
int main(){
	int ntest = 0;
	int sol[51] = {0,};
	cin >> ntest;

	for(int i = 0; i < ntest; i++){
		cin >> mapsize >> work;
		get_input();

		sol[i] = solve();
	}
	for(int i = 0 ; i < ntest; i++)
		cout << "#" << i+1 << " " << sol[i] << endl;
	return 0;
}
{% endhighlight %}