---
layout: post
title:  "2112. [모의 SW 역량테스트] 보호 필름"
subtitle: ""
date:   2019-07-21 16:34:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV5V1SYKAaUDFAWu"}})


## [풀이]

{% highlight c %}
#include <cstdio>
#include <iostream>
#include <deque>
#include <algorithm>
#include <string.h>
using namespace std;

#define MAXROW 13 + 2
#define MAXCOL 20 + 2
int map[MAXROW][MAXCOL];
int rowsize;
int colsize;
int kijoon;
int minimum = 999999;
void get_input(){
	memset(map, -1, sizeof(map));
	cin >> rowsize >> colsize >> kijoon;
	for(int i = 1 ; i <= rowsize ; i++)
		for(int j = 1 ; j <= colsize ; j++)
			cin >> map[i][j];
}
int valid_check(int col, int cmap[MAXROW][MAXCOL]){
	if(col){
		int state;
		for(int i = 1 ; i <= rowsize - kijoon + 1 ; i++){
			state = cmap[i][col];
			for(int j = i + 1 ; j <= i + kijoon - 1 ; j++){
				if(cmap[j][col] != state) break;
				if(j == i + kijoon - 1) return 1;
			}
		}
	}
	else{
		for(int j = 1 ; j <= colsize ; j++)
			if(valid_check(j, cmap) == 0)
				return 0;
		return 1;
	}
	return 0;
}
int dfs(int depth, int insert, int cmap[MAXROW][MAXCOL]){
	if(depth > rowsize) return 0;  // end of dfs
	if(minimum < insert) return 0; // if non-promising node
	
	deque<int> q;
	
	if(valid_check(0, cmap)){
		if(minimum > insert) minimum = insert;
		return insert;
	}else{
		int res = dfs(depth + 1, insert, cmap);
		if(res != 0)
			q.push_back(res);
	}

	int temp[MAXROW][MAXCOL];
	memcpy(temp, cmap, sizeof(temp));
	for(int i = 1 ; i <= colsize ; i++)
		temp[depth][i] = 0;

	if(valid_check(0, temp)){
		if(minimum > insert + 1)
			minimum = insert+1;
		return insert + 1;
	}else{
		int res = dfs(depth + 1, insert + 1, temp);
		if(res != 0)
			q.push_back(res);
	}
	
	int temp2[MAXROW][MAXCOL];
	memcpy(temp2, cmap, sizeof(temp2));
	for(int i = 1 ; i <= colsize ; i++)
		temp2[depth][i] = 1;

	if(valid_check(0, temp2)){
		if(minimum > insert + 1)
			minimum = insert+1;
		return insert + 1;
	}else{
		int res = dfs(depth + 1, insert + 1, temp2);
		if(res != 0)
			q.push_back(res);
	}

	if(q.size() == 0)
		return 0;
	else{
		sort(q.begin(), q.end());
		return q.at(0);
	}
}

int solve(){
	minimum = 999999;
	return dfs(1, 0, map);
}
int main(){
	int sol[50] = {0,};
	int ntest;
	cin >> ntest;
	for(int i = 0 ; i < ntest ; i++){
		get_input();
		sol[i] = solve();
	}
	for(int i = 0 ; i <ntest ; i++)
		cout << "#" << i+1 << " " << sol[i] << endl;
}
{% endhighlight %}