---
layout: post
title:  "1238. [S/W 문제해결 기본] 10일차 - Contact"
subtitle: ""
date:   2019-10-20 12:24:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV15B1cKAKwCFAYD"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <deque>
using namespace std;
int len, sol, from, to, start;
#define MAXN 101
deque<int> contact[MAXN];
deque<int> q;
int visit[MAXN];
int _max;
void solve() {
	int node;
	q.push_back(start);
	deque<int> nextq;
	int count = 0;

	while (!q.empty()) {
		bool flag = true;
		int maxnode = 0;

		while (!q.empty()) {
			node = q[0]; q.pop_front();

			maxnode = node > maxnode ? node : maxnode;
			visit[node] = 1;
			for (int i = 0; i < contact[node].size(); i++) {
				if (visit[contact[node][i]] == 0) {
					visit[contact[node][i]] = 1;
					nextq.push_back(contact[node][i]);
					flag = false;
				}
			}
		}
		if (flag) {
			_max = maxnode;
			return;
		}
		q = nextq;
		nextq.clear();
	}
}

int main() {
	for (int i = 1; i <= 10; ++i) {
		_max = 0;
		for (int i = 0; i < MAXN; i++) {
			contact[i].clear();
			visit[i] = 0;
		}
		cin >> len >> start;
		for (int i = 0; i < len / 2; i++) {
			cin >> from >> to;
			contact[from].push_back(to);
		}
		solve();
		cout << "#" << i << " " << _max << "\n";
	}
}
{% endhighlight %}