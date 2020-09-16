---
layout: post
title:  "1216. [S/W 문제해결 기본] 3일차 - 회문2"
subtitle: ""
date:   2019-10-12 14:36:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV14Rq5aABUCFAYi"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <cstdio>
using namespace std;
#define MAXN 100
int T, Q, sol;
char MAP[MAXN][MAXN + 1];

#define INBOUND(i, length) (i + length - 1 < MAXN)
int _maxlen;

void solve() {
	for (int i = 0; i < MAXN; ++i) {
		for (int j = 0; j < MAXN; ++j) {
		
			for (int len =2; len <= 100 - j; len++) {
				// 가로로 len 길이만큼 탐색, 팰린드롬이 아닐 경우 즉시 탈출
				if (!INBOUND(j, len)) break;
				/*bool flag = false;*/
				for (int k = 0; k < len / 2; k++) {
					//printf("가로 : [%d, %d] [%d, %d]\n", i, j + k, i, j + len - k - 1);
					if (MAP[i][j + k] != MAP[i][j + len - k - 1]) {
						//flag = true;
						break;
					}
					if (k == len / 2 - 1) {
						_maxlen = len > _maxlen ? len : _maxlen;
					}
				}
				//if (flag) break;				
			}
	
		}
	}
	for (int i = 0; i < MAXN; ++i) {
		for (int j = 0; j < MAXN; ++j) {

			for (int len = 2; len <= 100 - j; len++) {
				// 세로로 len 길이만큼 탐색, 팰린드롬이 아닐 경우 즉시 탈출
				if (!INBOUND(j, len)) break;

				//bool flag = false;
				for (int k = 0; k < len / 2; k++) {
					//printf("가로 : [%d, %d] [%d, %d]\n", j + k, i, j + len - k -1, i);

					if (MAP[j + k][i] != MAP[j + len - k - 1][i]) {
						//flag = true;
						break;
					}
					if (k == len / 2 - 1) {
						_maxlen = len > _maxlen ? len : _maxlen;
					}
				}
				//if (flag) break;
			}
		}
	}
}

int main() {
	T = 10;
	for (int i = 1; i <= 10; ++i) {
		cin >> Q;
		for (int i = 0; i < MAXN; ++i) {
			cin >> MAP[i];
		}
		_maxlen = 1;
		solve();
		printf("#%d %d\n", i, _maxlen);
	}
}
{% endhighlight %}