---
layout: post
title:  "4168. 삼성이의 쇼핑 ( 비트마스크 + 조합 연습 )"
subtitle: ""
date:   2019-09-06 15:22:08 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/userProblem/userProblemDetail.do?contestProbId=AWKEgExqDGMDFAS-"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <cstring>
#include <cstdio>
#define DEBUG 0
using namespace std;
#define MAXM 26
int N, M;
int cloth[2][MAXM]; // [0] : price, [1] : satisfaction
int satmax = 0;
int sol_index[MAXM];
void get_input(){
	satmax = 0;
	memset(sol_index, 0, sizeof(sol_index));
	memset(cloth, 0, sizeof(cloth));
	cin >> N >> M;
	for(int i = 0 ; i < M ;i++){
		cin >> cloth[0][i] >> cloth[1][i];
	}
}
void dfs(int depth, int cost, int sat, int bitmask[MAXM]){
	if(depth > M)
		return;
	int temp1[MAXM];
	int temp2[MAXM];
	memcpy(temp1, bitmask, sizeof(temp1));
	memcpy(temp2, bitmask, sizeof(temp2));

	temp1[depth] = 1;
	if(cost + cloth[0][depth] < N){
		if(sat + cloth[1][depth] > satmax){
			satmax = sat + cloth[1][depth];
			memcpy(sol_index, temp1, sizeof(sol_index));
		}
		dfs(depth+1, cost + cloth[0][depth], sat + cloth[1][depth], temp1);
	}

	temp2[depth] = 0;
	dfs(depth+1, cost, sat, temp2);

}
int solve(){
	int bitmask[MAXM];
	memset(bitmask, 0, sizeof(bitmask));
	dfs(0, 0, 0, bitmask);
	return satmax;
}
int main(){
	int sol[50] = {0,};
	int ntest;
	cin >> ntest;
	for(int i = 0 ; i < ntest ; i++){
		get_input();
		sol[i] = solve();
		cout << "#" << i + 1 << " ";
		for(int i = 0 ; i < M; i++)
			if(sol_index[i] == 1)
				cout << i << " ";
		cout << sol[i] << endl;
		//sol[i] = solve();
	}
	//for(int i = 0 ; i <ntest ; i++)
	//	cout << "#" << i+1 << " " << sol[i] << endl;
}
{% endhighlight %}