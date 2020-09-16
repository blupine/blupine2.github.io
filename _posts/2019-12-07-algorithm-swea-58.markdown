---
layout: post
title:  "1245. [S/W 문제해결 응용] 2일차 - 균형점"
subtitle: ""
date:   2019-12-07 13:55:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV15MeBKAOgCFAYD"}})

## [풀이]

{% highlight c %}
#include <stdio.h>
#define ABS(a) ((a) < 0 ? (a)*-1 : (a))
int T, N;
int lclose, rclose;

int mag[11];
int weight[11];
double sol[11];

double parameteric_search(double start, double end) {
	
	double mid = (start + end) / 2, dis, ddis;
	double sum_left = 0, sum_right = 0;
	
	for (int i = 1; i <= (int)lclose; i++) {
		dis = (double)(mag[i] - mid);
		ddis = weight[i] / (dis * dis);
		sum_left += ddis;
	}
	for (int i = rclose; i <= N; i++) {
		dis = (double)(mag[i] - mid);
		ddis = weight[i] / (dis * dis);
		sum_right += ddis;
	}

	if (ABS(start - end) < 1e-11) return mid;
	if (sum_left > sum_right)
		return parameteric_search(mid + 1e-12, end);
	else
		return parameteric_search(start, mid);
}

int main() {
	scanf("%d", &T);
	for (int i = 1; i <= T; ++i) {
		scanf("%d", &N);
		for (int j = 1; j <= N; j++)
			scanf("%d", &mag[j]);
		for (int j = 1; j <= N; j++)
			scanf("%d", &weight[j]);

		printf("#%d ", i);

		for (int i = 1; i <= N - 1; i++) {
			// 0 ~ i 와 i+1~N 사이에서 중간점을 찾아야 함
			lclose = i, rclose = i + 1;
			sol[i] = parameteric_search(mag[i], mag[i + 1]);
			printf("%.10f ", sol[i]);
		}
		printf("\n");
	}
}
{% endhighlight %}