---
layout: post
title:  "1217. [S/W 문제해결 기본] 4일차 - 거듭 제곱"
subtitle: ""
date:   2019-10-14 13:22:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV14dUIaAAUCFAYD"}})

## [풀이]

{% highlight c %}
#include <iostream>
using namespace std;

int T, N, M, sol;

void solve(int depth, int num) {
	if (depth >= M) {
		sol = num;
		return;
	}
	solve(depth + 1, num * N);
}

int main() {
	for (int i = 1; i <= 10; i++) {
		cin >> T;

		cin >> N >> M;

		solve(1, N);

		cout << "#" << T << " " << sol << "\n";
	}
}
{% endhighlight %}