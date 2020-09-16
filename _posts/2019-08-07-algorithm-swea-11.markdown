---
layout: post
title:  "4008. [모의 SW 역량테스트] 숫자 만들기"
subtitle: ""
date:   2019-08-07 14:03:37 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWIeRZV6kBUDFAVH"}})


## [풀이]

{% highlight c %}
#include <iostream>
#include <vector>
#include <string.h>
using namespace std;
int N;
vector<int> numbers;
int opcount[4] = {0,};
int totalop = 0;
int maximum = -100000;
int minimum = 100000;
void get_input(){
	memset(opcount, 0, sizeof(opcount));
	totalop = 0;
	maximum = -100000;
	minimum = 100000;
	numbers.clear();
	cin >> N;
	int op, num;
	for(int i = 0 ; i < 4; i++){
		cin >> opcount[i];
		totalop += opcount[i];
	}
	for(int i = 0 ; i < N ; i++){
		cin >> num;
		numbers.push_back(num);
	}
}
int calculate(vector<int> op){
	vector<int> num = numbers;
	int res = 0;
	for(int i = 0 ; i < op.size(); i++){
		switch(op[i]){
			case 0:
				res = num[i] + num[i+1];
				break;
			case 1:
				res = num[i] - num[i+1];
				break;
			case 2:
				res = num[i] * num[i+1];
				break;
			case 3:
				res = num[i] / num[i+1];
				break;
		}		
		num[i+1] = res;
	}
	return res;
}
void dfs(int depth, vector<int> op, int count[4]){
	if(op.size() == totalop){
		int res = calculate(op);
		if(res > maximum) maximum = res;
		if(res < minimum) minimum = res;
		return;
	}
	int temp[4];
	for(int i = 0 ; i < 4 ; i++){
		if(count[i] != 0){
			vector<int> tmpop = op;
			memcpy(temp, count, sizeof(temp));
			temp[i]--;
			tmpop.push_back(i);
			dfs(depth+1, tmpop, temp);
 		}
	}
}
int main(){
	int sol[50] = {0,};
	int ntest;
	cin >> ntest;
	for(int i = 0 ; i < ntest ; i++){
		get_input();
		vector <int> null;
		dfs(0, null, opcount);
		sol[i] = maximum - minimum;
	}
	for(int i = 0 ; i <ntest ; i++)
		cout << "#" << i+1 << " " << sol[i] << endl;
}
{% endhighlight %}