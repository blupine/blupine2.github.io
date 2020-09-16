---
layout: post
title:  "4013. [모의 SW 역량테스트] 특이한 자석"
subtitle: ""
date:   2019-08-18 18:55:13 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWIeV9sKkcoDFAVH"}})


## [풀이]

{% highlight c %}
#include <iostream>
#include <deque>
#include <utility>
#include <math.h>

#define RIGHT_INDEX 2
#define LEFT_INDEX  6 
using namespace std;
typedef pair<int, int> Rotate; // index, dir
int K;
deque<int> magnetic[4]; // 0이면 N, 1이면 S
deque<Rotate> rot;
void get_input(){
	rot.clear();
	for(int i = 0 ; i < 4 ; i++)
		magnetic[i].clear();
	cin >> K;
	int temp;
	int topni, dir;
	for(int i = 0 ; i < 4 ; i++){
		for(int j = 0 ; j < 8 ; j++){
			cin >> temp;
			magnetic[i].push_back(temp);
		}
	}
	for(int i = 0 ; i < K ; i++){
		cin >> topni >> dir;
		rot.push_back(Rotate(topni, dir));
	}
}
void shift_magnetic(int index, int dir){
	if(dir == 1){ // 시계 방향
		int last = magnetic[index][7];
		magnetic[index].push_front(last);
		deque<int>::iterator iter = magnetic[index].end() - 1;
		magnetic[index].erase(iter);

	}
	else{ // 시계 반대 방향
		int first = magnetic[index][0];
		magnetic[index].push_back(first);
		magnetic[index].pop_front();
	}
}
int solve(){
	for(int i = 0 ; i < rot.size() ; i++){		int mag_index = rot[i].first - 1;
		int dir = rot[i].second;
		int check[4] = {0,};
		check[mag_index] = dir; // 방향 기록
		for(int j = mag_index + 1 ; j < 4 ; j++){
			if(magnetic[j][LEFT_INDEX] != magnetic[j-1][RIGHT_INDEX]){
				check[j] = -check[j-1];
			}else
				break;
		}
		for(int j = mag_index - 1 ; j >= 0 ; j--){
			if(magnetic[j][RIGHT_INDEX] != magnetic[j+1][LEFT_INDEX]){
				check[j] = -check[j+1];
			}else{
				break;
			}
		}

		for(int i = 0 ; i < 4 ; i++){
			if(check[i] != 0)
				shift_magnetic(i, check[i]);
		}
	}
	int res = 0;
	for(int i = 0 ; i < 4 ; i++)
		if(magnetic[i][0] == 1)
			res += pow(2, i);
	return res;
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