---
layout: post
title:  "1213. [S/W 문제해결 기본] 3일차 - String"
subtitle: ""
date:   2019-10-10 12:11:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV14P0c6AAUCFAYi"}})

## [풀이]

{% highlight c %}
#include <iostream>

#define MAXN 1001
using namespace std;
int T, Q, sol;

char Str[MAXN];
char Tar[11];

int _strlen(char * str) {
	int len = 0;
	while (*str++ != '\0') len++;
	return len;
}

void solve() {
	int index = 0;
	int tlen = _strlen(Tar);
	int Slen = _strlen(Str);
	while (index < Slen - tlen + 1) {
		for (int i = 0; i < tlen; ++i) {
			if (Tar[i] != Str[index + i]) {
				break;
			}
			if (i == tlen - 1) {
				sol++;
			}
		}
		index++;

	}
}

int main() {
	T = 10;
	for (int i = 1; i <= 10; ++i) {
		sol = 0;
		scanf("%d", &Q);
		scanf("%s", Tar);
		scanf("%s", Str);

		solve();
		printf("#%d %d\n", Q, sol);
	}
}
{% endhighlight %}