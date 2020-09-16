---
layout: post
title:  "4014. [모의 SW 역량테스트] 활주로 건설"
subtitle: ""
date:   2019-08-21 20:55:13 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWIeW7FakkUDFAVH"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <cstring>
#include <cmath>
using namespace std;
#define MAXMAP 22
int N, X;
float map[MAXMAP][MAXMAP];
void get_input(){
	memset(map, 0, sizeof(map));
	cin >> N >> X;
	for(int i = 1 ; i <= N ;i++)
		for(int j = 1 ; j <= N ; j++)
			cin >> map[i][j];
}
int build(float arr[MAXMAP]){
	for(int i = 1 ; i < N ; i++){
		if(abs(arr[i] - arr[i+1]) >= 2 ) 
			return 0;
		if(arr[i] < arr[i+1] && arr[i] - int(arr[i]) > 0) // 현재가 내리막길이고 다음에 높은게 나올때
			return 0;
		if(arr[i] - arr[i+1] < -0.6){ // arr[i+1]이 더 클때
			for(int j = 0 ; j < X ; j++) // X 길이만큼의 다리를 왼쪽으로 놓을 수 있는지 확인
				if(arr[i] != arr[i - j])
					return 0;
			for(int j = 0 ; j < X ; j++) // X 길이만큼의 다리를 왼쪽으로 놓음
				arr[i - j] += 0.5;
		}
		else if(arr[i] - arr[i+1] > 0.6){ // arr[i+1]이 더 작을 때  
			for(int j = 0 ; j < X ; j++) // X 길이만큼의 다리를 오른쪽으로 놓을 수 있는지 확인
				if(arr[i+1] != arr[i + 1 + j])
					return 0;
			for(int j = 0 ; j < X ; j++) // X 길이만큼의 다리를 놓음
				arr[i + 1 + j] += 0.5;
		}
	}
	return 1;
}
int solve(){
	int count = 0;
	float temp[MAXMAP];
	for(int i = 1 ; i <= N ; i++){
		memcpy(temp, map[i], sizeof(temp));
		if(build(temp) == 1)
			count++;
	}
	memset(temp, 0, sizeof(temp));
	for(int i = 1 ; i <= N ; i++){
		for(int j = 0 ; j < MAXMAP ; j++)
			temp[j] = map[j][i];
		if(build(temp) == 1)
			count++;
		
	}
	return count;
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