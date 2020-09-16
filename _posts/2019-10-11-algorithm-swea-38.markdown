---
layout: post
title:  "1213. [S/W 문제해결 기본] 3일차 - String"
subtitle: ""
date:   2019-10-11 13:23:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV14QpAaAAwCFAYi"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <cstdio>
using namespace std;
#define MAXN 9
int T, Q, sol, len;
char MAP[9][MAXN];

#define INBOUND(i) (i + len - 1 < 8)


void solve() {
	for (int i = 0; i < MAXN - 1; ++i) {
		for (int j = 0; j < MAXN - 1; ++j) {
			//printf("i, j : %d, %d\n", i, j);
			for (int k = 0; k < len / 2; k++) {
				if (!INBOUND(j)) break;
				//printf("\tcmp 가로 : [%d, %d], [%d, %d]\n", i, j + k, i, j + len - k - 1);

				if (MAP[i][j + k] != MAP[i][j + len - k - 1]) break;// 가로
				if (k == len / 2 - 1){
					//printf("\t  founc : sol[%d] -> [%d]\n", sol, sol + 1);

					sol++;
				}
			}
			for (int k = 0; k < len / 2; k++) {
				if (!INBOUND(i)) break;
				//printf("\tcmp 세로 : [%d, %d], [%d, %d]\n", i+k, j , i + len - k - 1, j);

				if (MAP[i + k][j] != MAP[i + len - k - 1][j]) break; // 세로
				if (k == len / 2 - 1) {
					//printf("\t  founc : sol[%d] -> [%d]\n", sol, sol + 1);

					sol++;
				}
			}

		}
	}
}

int main() {
	T = 10;
	for (int i = 1; i <= 10; ++i) {
		sol = 0;
		cin >> len;
		for (int i = 0; i < MAXN - 1; ++i) {
				cin >> MAP[i];
		}
		if (len == 1) sol = 64;
		else solve();
		printf("#%d %d\n", i, sol);
	}
}
{% endhighlight %}