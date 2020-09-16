---
layout: post
title:  "1209. [S/W 문제해결 기본] 2일차 - Sum"
subtitle: ""
date:   2019-10-09 14:31:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV13_BWKACUCFAYh"}})

## [풀이]

{% highlight c %}
#include <iostream>
using namespace std;
#define MAXN 102
int MAP[MAXN][MAXN];
int T, N, sol, sum;
int _max;
void solve() {
	for (int i = 1; i <= 100; ++i) {
		sum = 0;
		for (int j = 1; j <= 100; ++j) {
			sum += MAP[j][i];
		}
		MAP[101][i] = sum;
		_max = sum > _max ? sum : _max;
	}
	sum = 0;
	int sum2 = 0;
	for (int i = 1; i <= 100; ++i) {
		sum += MAP[i][i];
		sum2 += MAP[101 - i][101 - i];
	}
	_max = sum > _max ? sum : _max;
	_max = sum2 > _max ? sum2 : _max;	
}

int main() {
	T = 10;
	for (int i = 1; i <= T; ++i) {
		cin >> N;
		_max = 0;
		for (int j = 1; j <= 100; ++j) {
			sum = 0;
			for (int K = 1; K <= 100; ++K) {
				cin >> MAP[j][K];
				sum += MAP[j][K];
			}
			MAP[j][101] = sum;
			_max = sum > _max ? sum : _max;
		}

		solve();
		cout << "#" << i << " " << _max << "\n";
	}
}
{% endhighlight %}