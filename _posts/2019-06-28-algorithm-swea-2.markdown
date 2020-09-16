---
layout: post
title:  "1767. [SW Test 샘플문제] 프로세서 연결하기"
subtitle: ""
date:   2019-06-28 18:21:32 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV4suNtaXFEDFAUf"}})


## [풀이]

{% highlight c %}

#include <iostream>
#include <vector>
#include <utility>
#include <algorithm>

#define DEBUG 0
#define MAXMAP 14
using namespace std;
vector<pair<int, int> > procs;
int map[MAXMAP][MAXMAP];
int nsize = 0;
int dir[4][2] = { {0, 1}, {0, -1}, {1, 0}, {-1, 0} }; // 동, 서, 남, 북

int max_connection = 0;

void clear_map(){
	procs.clear();
	max_connection = 0;
	for(int i = 0 ; i < MAXMAP; i++)
		for(int j = 0 ; j < MAXMAP ; j++)
			map[i][j] = -1;
}

void get_input(){
	clear_map();
	cin >> nsize;
	for(int i = 1 ; i <= nsize ; i++){
		for(int j = 1 ; j <= nsize ; j++){
			cin >> map[i][j];
			if(map[i][j] == 1){ // if input is processor
				if(i > 1 && i < nsize && j > 1 && j < nsize) 
					procs.push_back(pair<int, int> (i, j));
			}
		}
	}
	procs.push_back(make_pair(-1, -1));
}
int get_distance(int direction, pair<int, int> loc, int copy_map[MAXMAP][MAXMAP]){ // return distance from location to wall, if not valid, return -1
	int row = loc.first;
	int col = loc.second;
	int distance = 0;
	for(int i = 1 ; i <= nsize; i++){
		int newrow = row + dir[direction][0] * i;
		int newcol = col + dir[direction][1] * i;
		if(copy_map[newrow][newcol] == -1)
			break;

		if(copy_map[newrow][newcol] == 1 || copy_map[newrow][newcol] == 2){
			distance = 0;
			break;
		}
		distance += 1;
	}
	return distance;
} 

pair<int, int> dfs(int depth, int conn, pair<int, int> proc, int cmap[MAXMAP][MAXMAP]){
	if(depth >= procs.size()-1) 
		return make_pair(conn, 0);
	if(conn > max_connection) max_connection += 1;

	vector<pair<int, int> > next; // vector of pair<distance, direction>
	for(int i = 0; i < 4 ; i++){
		int distance = get_distance(i, proc, cmap);
		if(distance != 0){
			next.push_back(make_pair(distance, i));
		}
	}
	sort(next.begin(), next.end()); // search shortest distance first
	next.push_back(make_pair(0, 4)); // case of no connection

	vector<pair<int, int> > candidate;

	for(int i = 0 ; i < next.size(); i++){
		int distance = next.at(i).first;
		int direction = next.at(i).second;
 	
		int copy_map[MAXMAP][MAXMAP]; // save before status
		for(int j = 0 ; j < MAXMAP; j++)
			for(int k = 0 ; k < MAXMAP; k++)
				copy_map[j][k] = cmap[j][k];

		for(int j = 0 ; j < distance; j++){
			int newrow = proc.first + dir[direction][0] * (j+1);
			int newcol = proc.second + dir[direction][1] * (j+1); 
			copy_map[newrow][newcol] = 2;
		}
		
		int connection = conn;

		if(distance != 0)
			connection += 1;

		// first, compare connection, if bigger than other node, select it and update distane to ret_val
		pair<int, int> res = dfs(depth + 1, connection, procs.at(depth+1), copy_map);
		res.second += distance;		
		
		candidate.push_back(res);
		
		if(distance == 1){ // just connect to wall and do not check others 
			break;
		}
	}

	pair<int, int> ret;
	sort(candidate.begin(), candidate.end());
	ret = candidate.back();
	
	for(int i = 0 ; i < candidate.size() ; i++){
		if(candidate.at(i).first == ret.first){
			ret = candidate.at(i);
			break;
		}
	}

	return ret;
}
int main(){
	int ntest = 0;
	int sol[51] = {0,};
	cin >> ntest;

	for(int i = 0; i < ntest; i++){
		get_input();
		sol[i] = dfs(0, 1, procs.at(0), map).second;
	}
	for(int i = 0 ; i < ntest; i++)
		cout << "#" << i+1 << " " << sol[i] << endl;
	return 0;
}

{% endhighlight %}