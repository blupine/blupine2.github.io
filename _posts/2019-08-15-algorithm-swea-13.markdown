---
layout: post
title:  "4012. [모의 SW 역량테스트] 요리사"
subtitle: ""
date:   2019-08-15 17:37:13 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWIeUtVakTMDFAVH"}})


## [풀이]

{% highlight c %}
#include <iostream>
#include <vector>
#include <utility>
#include <string.h>

#define MAXMAP 18
using namespace std;
int map[MAXMAP][MAXMAP] = {-1,};
int N;
int imum = 999999;
void get_input(){
	imum = 999999;
	memset(map, -1, sizeof(map));
	cin >> N;
	for(int i = 1 ; i <= N ; i++){
		for(int j = 1 ; j <= N ; j++){
			cin >> map[i][j];
		}
	}
}
int dfs(int depth, vector<int> A, vector<int> B){ // depth starts at 1
	if(A.size() == N/2 && B.size() == N/2){
		int tasteA = 0;
		int tasteB = 0;
		for(int i = 0 ; i < A.size() - 1 ; i++){
			for(int j = i + 1 ; j < A.size(); j++)
				tasteA += map[A[i]][A[j]] + map[A[j]][A[i]];
		}
		for(int i = 0 ; i < B.size() - 1 ; i++){
			for(int j = i + 1 ; j < B.size(); j++)
				tasteB += map[B[i]][B[j]] + map[B[j]][B[i]];
		}
		int res = abs(tasteA - tasteB);
		if(res < imum) imum = res;
	}
	if(A.size() < N/2){
		vector<int> newA = A;
		vector<int> newB = B;
		newA.push_back(depth);
		dfs(depth + 1, newA, newB);
	}
	if(B.size() < N/2){
		vector<int> newA = A;
		vector<int> newB = B;
		newB.push_back(depth);
		dfs(depth + 1, newA, newB);
	}

}
int main(){
	int sol[50] = {0,};
	int ntest;
	cin >> ntest;
	for(int i = 0 ; i < ntest ; i++){
		get_input();
		vector<int> null1, null2;
		dfs(1, null1, null2);
		sol[i] = imum;
	}
	for(int i = 0 ; i <ntest ; i++)
		cout << "#" << i+1 << " " << sol[i] << endl;
}
{% endhighlight %}