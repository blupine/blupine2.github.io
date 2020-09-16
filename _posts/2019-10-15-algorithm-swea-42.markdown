---
layout: post
title:  "1219. [S/W 문제해결 기본] 4일차 - 길찾기"
subtitle: ""
date:   2019-10-15 12:03:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV14geLqABQCFAYD"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <vector>
#define MAXN 100
using namespace std;

vector<int> map[MAXN];
vector<int> visit(100);
int T, N, L, sol, A, B;

void dfs(int depth, int cur) {
	if (cur == 99) {
		sol = 1;
		return;
	}

	if (map[cur].size() == 0) return;

	for (int i = 0; i < map[cur].size(); i++) {
		int next = map[cur][i];
		if (visit[next] != 1) {
			visit[next] = 1;
			dfs(depth + 1, next);
			visit[next] = 0;
		}
	}

}

int main() {
	for (int i = 1; i <= 10; i++) {
		sol = 0;
		for (int i = 0; i < MAXN; i++) {
			map[i].clear();
			visit[i] = 0;
		}
		cin >> T;
		cin >> L;
		for (int j = 0; j < L; ++j) {
			cin >> A >> B;
			map[A].push_back(B);
		}
		visit[0] = 1;
		dfs(0, 0);

		cout << "#" << T << " " << sol << "\n";
	}
	return 0;
}
{% endhighlight %}