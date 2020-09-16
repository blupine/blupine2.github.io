---
layout: post
title:  "2115. [모의 SW 역량테스트] 벌꿀채취"
subtitle: ""
date:   2019-07-14 17:05:04 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV5V4A46AdIDFAWu"}})


## [풀이]

{% highlight c %}
#include <iostream>
#include <deque>
#include <string.h>
#include <utility>
#include <algorithm>
#define MAXMAP 12
using namespace std;
typedef pair<int, int> pos;
int map[MAXMAP][MAXMAP] = {-1,};
int n, m, c;
int get_max_dfs(int index, deque<int> honeys, int arr[5]){
	if(index > m){
		int res = 0;
		for(int i = 0 ; i < honeys.size() ; i++)
			res += honeys.at(i)*honeys.at(i);
		return res;
	}
	int res[2] = {0,};
	int sum = 0;
	for(int i = 0 ; i < honeys.size(); i++)
		sum += honeys.at(i);
	if(sum + arr[index] <= c){
		deque<int> temp(honeys);
		temp.push_back(arr[index]);
		res[0] = get_max_dfs(index + 1, temp, arr);
	}
	res[1] = get_max_dfs(index + 1, honeys, arr);
	return max(res[1], res[0]);
}
int get_max(int arr[5]){
	deque<int> nulld;
	int res = get_max_dfs(0, nulld, arr);
	return res;
}
int get_input(){
	memset(map, -1, sizeof(map));
	cin >> n >> m >> c;
	for(int i = 1 ; i <= n ; i++)
		for(int j = 1 ; j <= n ; j++)
			cin >> map[i][j];
}
int solve(){
	deque<pair<int, pos> > candidate;
	int beoltong[5] = {-1, };
	for(int i = 1; i <= n ; i++){
		for(int j = 1; j <= n - m + 1; j++){
			memset(beoltong, 0, sizeof(beoltong));			
			memcpy(beoltong, &map[i][j], sizeof(int) * m);
			int max = get_max(beoltong);
			candidate.push_back(pair<int, pos> (max, pos(i, j)));
		}
	}
	sort(candidate.begin(), candidate.end());
	int first_val = candidate.back().first;
	pos first_pos = candidate.back().second;
	int second_val;// = candidate.at(0).first;
	pos second_pos;// = candidate.at(0).second;
	
	for(int i = candidate.size() - 2 ; i >= 0; i--){
		pos temp = candidate.at(i).second;
		if(temp.first != first_pos.first || 
			(temp.second >= first_pos.second + m || temp.second + m  <= first_pos.second)){
			second_val = candidate.at(i).first;
			second_pos = candidate.at(i).second;
			break;
		}
	}
	return first_val + second_val;
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