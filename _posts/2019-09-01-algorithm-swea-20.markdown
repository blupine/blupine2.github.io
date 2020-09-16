---
layout: post
title:  "5658. [모의 SW 역량테스트] 보물상자 비밀번호"
subtitle: ""
date:   2019-09-01 16:57:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXRUN9KfZ8DFAUo"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <cstring>
#include <vector>
#include <cmath>
#include <algorithm>

using namespace std;

int N, K, C;
char input[29];

int hexstr_to_int(char * hexstr){
	int hex = 0;
	for(int i = 0 ; i < C ;i++){
		int postfix = hexstr[i] >= 'A' ? 55 : 48;
		hex += pow(16, C-i-1) * (hexstr[i] - postfix);
	}
	return hex;
}
bool compare(const int & num1, const int & num2){
	return num1 > num2;
}
void get_input(){
	cin >> N >> K;
	cin >> input;
	C = N / 4;
}
int is_in_vector(vector<int> vect, int num){
	for(int i = 0 ; i < vect.size() ; i++)
		if(vect[i] == num)
			return 1;
	return 0;
}
int solve(){
	int count = 0;
	int rotate = 0;
	char  cpstr[29];
	vector<int> nums;
	while(count != K){
		int hex_num[4];
		for(int i = 0 ; i < N - rotate ; i++)
			cpstr[rotate + i] = input[i];
		for(int i = 0 ; i < rotate  ; i++)
			cpstr[i] = input[N - rotate + i];
		cpstr[N] = '\0';
		for(int i = 0 ; i < 4 ; i++){
			hex_num[i] = hexstr_to_int(cpstr + i * C);
			if(!is_in_vector(nums, hex_num[i]))
				nums.push_back(hex_num[i]);
		}

		if(rotate >= C- 1){
			sort(nums.begin(), nums.end(), compare);
			return nums[K-1];
		}
		rotate++;
	}
	return 0;
}

int main(){
	int sol[50] = {0,};
	int ntest;
	cin >> ntest;
	for(int i = 0 ; i < ntest ; i++){
		get_input();
		sol[i] = solve();
	}
	for(int i = 0 ; i < ntest ; i++)
		cout << "#" << i+1 << " " << sol[i] << endl;
}
{% endhighlight %}