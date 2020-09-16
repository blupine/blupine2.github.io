---
layout: post
title:  "3752. 가능한 시험 점수"
subtitle: ""
date:   2019-11-11 13:12:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWHPkqBqAEsDFAUn"}})

## [풀이]

{% highlight c %}
#include <iostream>
#define MAXN 101
using namespace std;

int _find[MAXN * 100];
int input[MAXN];
int _find2[MAXN * 100]; int _find2_top;

int T, N;


void solve() {
	_find2[_find2_top++] = 0;
	_find[0] = 1;

	for (int i = 1; i <= N; i++) {
		int f = _find2_top;
		for (int j = 0; j < _find2_top; j++) {
			if (_find[_find2[j] + input[i]] == 0) {
				_find[_find2[j] + input[i]] = 1;
				_find2[f++] = _find2[j] + input[i];
			}
		}
		_find2_top = f;
	}
}

int main() {
	cin >> T;
	for (int i = 1; i <= T; ++i) {
		_find2_top = 0;
		cin >> N;
		for (int i = 0; i < MAXN * 100; ++i) _find[i] = 0;
		//for (int i = 0; i < 11; ++i) _count[i] = 0;
		for (int j = 1; j <= N; ++j) {
			cin >> input[j];
		}
		solve();
		cout << "#" << i << " " << _find2_top << "\n";
	}

}
// 0 1 4 5 8 9
{% endhighlight %}