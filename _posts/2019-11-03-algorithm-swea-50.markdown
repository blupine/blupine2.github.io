---
layout: post
title:  "1233. [S/W 문제해결 기본] 9일차 - 사칙연산 유효성 검사"
subtitle: ""
date:   2019-11-03 16:21:52 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV141176AIwCFAYD"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <sstream>
#include <cstring>
#define MAXNODE 1001
using namespace std;

int N, A, L, R;
bool sol;
string B;
string input;

int buf[MAXNODE * 2][3];
bool isoperator(string c) { return !(c[0] <= '9' && c[0] >= '0'); }
int _atoi(string str) {
	if (isoperator(str)) {
		if (str[0] == '+') return -1;
		else if (str[0] == '-') return -2;
		else if (str[0] == '*') return -3;
		else return -4;
	}
	else {
		return stoi(str);
	}
}

void preorder(int node) {
	// leaf인데 숫자가 아닐 경우
	// 연속해서 숫자가 나올 경우
	if (!sol) return;

	if (buf[node][0] == 0) {
		sol = false;
		return;
	}

	if (buf[node][0] < 0 && buf[node][1] == 0 && buf[node][2] == 0){
		// leaf인데 숫자가 아닐 경우 // leaf는 왼쪽 오른쪽 자식이 없음
		sol = false;
		return;
	}

	if (buf[node][0] > 0 && (buf[node][1] != 0 && buf[node][2] != 0) && (buf[buf[node][1]] > 0 || buf[buf[node][2]] > 0)){
			sol = false;
			return;
	}

	if(buf[node][1] != 0)
		preorder(buf[node][1]);
	if(buf[node][2] != 0)
		preorder(buf[node][2]);
}

int main() {
	for (int i = 1; i <= 10; ++i) {
		cin >> N;
		sol = true;
		for (int i = 0; i < MAXNODE * 2; i++) buf[i][0] = buf[i][1] = buf[i][2] = 0;

		if (N % 2 == 0) sol = false;
		
		int bef = 0;
		getline(cin, input);
		for (int i = 1; i <= N; i++) {
			L = R = 0;
			getline(cin, input);
			stringstream ss(input);
			ss >> A >> B >> L >> R;
			if (!isoperator(B) && L != 0 && R != 0) sol = false;
			buf[A][0] = _atoi(B);
			if (isoperator(B)) {
				buf[A][1] = L;
				buf[A][2] = R;
			}
		}
		
		preorder(1);
		cout << "#" << i << " " << sol << "\n";

	}
}
{% endhighlight %}