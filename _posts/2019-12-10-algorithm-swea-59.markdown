---
layout: post
title:  "1244. [S/W 문제해결 응용] 2일차 - 최대 상금"
subtitle: ""
date:   2019-12-10 15:23:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV15Khn6AN0CFAYD"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <cstring>
#define SWAP(A, B) A=A^B; B=A^B; A=A^B;
using namespace std;
int T, N, sol, C, len;
char arr[7];
bool found[6];
bool visited[1000000][10];
int _strlen(char * str) {
	int len = 0;
	while (*str++ != '\0') len++;
	return len;
}

int to_int(char * str) {
	int ret = 0;
	while (*str) ret = ret * 10 + *str++ - '0';
	return ret;
}

void dfs(int count) {
	int sum = to_int(arr);
	if (count == 0) {
		sol = sum > sol ? sum : sol;
	}
	else {
		if (visited[sum][count] == 0) {
			visited[sum][count] = 1;
			for (int i = 0; i < len; i++) {
				for (int j = i; j < len; j++) {
					if (i == j) continue;
					SWAP(arr[i], arr[j]);
					dfs(count - 1);
					SWAP(arr[i], arr[j]);
				}
			}
		}

	}
}

// 87466
void init() {
	sol = 0;
	memset(visited, 0, sizeof(visited));
}

int main() {
	cin >> T;
	for (int i = 1; i <= T; i++) {
		init();
		
		cin >> arr >> C;
		len = _strlen(arr);
		
		dfs(C);

		cout << "#" << i << " " << sol << endl;
	}
}
{% endhighlight %}