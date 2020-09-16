---
layout: post
title:  "2117. [모의 SW 역량테스트] 홈 방범 서비스"
subtitle: ""
date:   2019-07-25 17:12:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV5V61LqAf8DFAWu"}})


## [풀이]

{% highlight c %}
#include <iostream>
#include <deque>
#include <utility>
#include <string.h>
using namespace std;
#define MAXMAP 22
#define GETCOST(k) (k*k + (k-1)*(k-1))
#define ABS(x) (x > 0 ? x : -x) 

typedef pair<int, int> pos;
int map[MAXMAP][MAXMAP] = {-1,};
int dir[4][2] = { {0, 1}, {0, -1}, {-1, 0}, {1, 0} };
int n, m, nhome;
deque<pos> home;

int get_input(){
	memset(map, -1, sizeof(map));
	home.clear();
	cin >> n >> m;
	for(int i = 1 ; i <= n ; i++)
		for(int j = 1 ; j <= n ; j++){
			cin >> map[i][j];
			if(map[i][j] == 1) home.push_back(pos(i, j));
		}
	nhome = home.size();
}
int get_k(pos x, pos y){
	return abs(x.first - y.first) + abs(x.second - y.second) + 1;
}
int get_home_count(int kmax, pos loc){
	deque<int> kval;
     
	kval.assign(kmax + 1, 0);
	for(int i = 0 ; i < home.size() ; i++){
		int d = get_k(loc, home.at(i));
		if(d <= kmax)
			kval[d]++; // [0, 1, 1, 3, 0];
		
	}
	deque<int> home_count = kval;
	for(int i = 1 ; i <= home_count.size() ; i++)
		home_count[i] += home_count[i-1];
	
	int ret = 0;
	for(int i = 1 ; i <= kmax ; i++){
		int cost = GETCOST(i);
		if(cost <= home_count[i] * m){
			ret = home_count[i];
		}
	}
	return ret;
}
int solve(){
	int kmax = 0;
	while(1){
		int cost = GETCOST(kmax);
		if(cost >= nhome * m || kmax > n + 1){
			kmax--;
			break;
		}
		kmax++;
	}

	int max = 0;
	for(int i = 1 ; i <= n ; i++){
		for(int j = 1 ; j <= n ; j++){
			int temp = get_home_count(kmax, pos(i, j));
			if(temp == nhome)
				return temp;
			if(temp > max) max = temp;
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
	for(int i = 0 ; i <ntest ; i++)
		cout << "#" << i+1 << " " << sol[i] << endl;	
}
{% endhighlight %}