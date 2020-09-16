---
layout: post
title:  "1865. 동철이의 일 분배"
subtitle: ""
date:   2019-11-15 14:58:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV5LuHfqDz8DFAXc"}})

## [풀이]

{% highlight c %}
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#define MAXN 17
int T, N;
double sol;
int pmap[MAXN][MAXN];
int visit[MAXN];


void dfs(int depth, double prob) {
	if (depth > N) {
		sol = prob > sol ? prob : sol;
		return;
	}
	else if (prob < sol) return;
	else {
		
		for (int i = 1; i <= N; ++i) {
			if (visit[i] == 0 && pmap[depth][i] != 0) {
				visit[i] = 1;
				dfs(depth + 1, prob * (double)pmap[depth][i] / 100);
				visit[i] = 0;
			}
		}
	
	}
}

int main() {
	scanf("%d", &T);
	for (int i = 1; i <= T; ++i) {
		sol = 0;
		scanf("%d", &N);
		for (int j = 1; j <= N; ++j) {
			visit[j] = 0;
			for (int k = 1; k <= N; ++k) {
				scanf("%d", &pmap[j][k]);
			}
		}

		dfs(1, 1.0);

		printf("#%d %.6f\n", i, sol * 100);
	}
}
{% endhighlight %}