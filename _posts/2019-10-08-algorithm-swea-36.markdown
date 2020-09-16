---
layout: post
title:  "1220. [S/W 문제해결 기본] 5일차 - Magnetic"
subtitle: ""
date:   2019-10-08 12:31:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV14hwZqABsCFAYD"}})

## [풀이]

{% highlight c %}
#include <iostream>
using namespace std;
#define MAXN 101
int MAP[MAXN][MAXN];
int T, N, sol;

int solve() {
	int sol = 0;
	for (int i = 1; i <= N; ++i) {
		bool check = false;
		for (int j = 1; j <= N; ++j) {
			if (check && MAP[j][i] == 2) {
				sol++;
				check = false;
			}

			if (MAP[j][i] == 1) check = true;
		}
	}
	return sol;
}

int main() {
	T = 10;
	for (int i = 1; i <= T; ++i) {
		cin >> N;
		for (int j = 1; j <= N; ++j) {
			for (int K = 1; K <= N; ++K) {
				cin >> MAP[j][K];
			}
		}

		sol = solve();
		cout << "#" << i << " " << sol << "\n";
	}
}
{% endhighlight %}