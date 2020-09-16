---
layout: post
title:  "1210. [S/W 문제해결 기본] 2일차 - Ladder1"
subtitle: ""
date:   2019-09-11 13:59:08 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV14ABYKADACFAYh"}})

## [풀이]

{% highlight c %}
#include <stdio.h>
#include <iostream>
#include <cstring>
#define MAXN 100
#define INBOUND(i, j) ((i >= 0) && (i <= 99) && (j <= 99) && (j >= 0))
using namespace std;
int T, Q, sol;

int MAP[MAXN][MAXN];
int visit[MAXN][MAXN];
int pos[2];
int dir[3][2] = { {0, -1}, {0, 1}, {-1, 0} }; // 좌, 우, 상

void solve() {
	int next[2];
	while (pos[0] != 0) {
		visit[pos[0]][pos[1]] = 1;
		for (int i = 0; i < 3; ++i) {
			next[0] = pos[0] + dir[i][0];
			next[1] = pos[1] + dir[i][1];
			if (INBOUND(next[0], next[1]) && visit[next[0]][next[1]] != 1 && MAP[next[0]][next[1]] == 1) {
				pos[0] = next[0];
				pos[1] = next[1];
				break;
			}

		}
	}

	sol = pos[1];
}

int main() {
	T = 10;
	for (int i = 1; i <= 10; ++i) {
		memset(visit, 0, sizeof(visit));
		cin >> Q;
		for(int i = 0 ; i < MAXN ; ++i){
			for (int j = 0; j < MAXN; ++j) {
				cin >> MAP[i][j];
				if (MAP[i][j] == 2) {
					pos[0] = i; pos[1] = j;
				}
			}
		}
		solve();
		cout << "#" << Q << " " << sol << "\n";
	}
}
{% endhighlight %}