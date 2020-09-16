---
layout: post
title:  "6109. 추억의 2048게임"
subtitle: ""
date:   2019-11-07 17:32:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWbrg9uabZsDFAWQ"}})

## [풀이]

{% highlight c %}
#include <iostream>
#define MAXN 21
using namespace std;

int MAP[MAXN][MAXN];
int N, T;
char input[10];

void rotate_left(int MAP[MAXN][MAXN]) {
	int temp[MAXN][MAXN];
	for (int i = 1; i <= N; i++) for (int j = 1; j <= N; j++) 
		temp[i][j] = MAP[j][N - i + 1];
	for (int i = 1; i <= N; i++) for (int j = 1; j <= N; j++)
		MAP[i][j] = temp[i][j];
}
void rotate_right(int MAP[MAXN][MAXN]) {
	int temp[MAXN][MAXN];
	for (int i = 1; i <= N; i++) for (int j = 1; j <= N; j++) 
		temp[i][j] = MAP[N - j + 1][i];
	for (int i = 1; i <= N; i++) for (int j = 1; j <= N; j++)
		MAP[i][j] = temp[i][j];
}

void up(int MAP[MAXN][MAXN]) {
	bool found;
	int before, frow, fcol;
	for (int i = 1; i <= N; ++i) {
		found = false;
		for (int j = 1; j <= N; ++j) {
			if (!found && MAP[j][i] > 0) {
				found = true;
				frow = j; fcol = i;
				before = MAP[j][i];
			}
			else if (found && MAP[j][i] == before) {
				MAP[j][i] = 0;
				MAP[frow][fcol] *= 2;
				found = false;
			}
			else if(found && MAP[j][i] != 0) {
				before = MAP[j][i];
				frow = j; fcol = i;
			}
		}
		// 정리해줘야 함
		int top = 1;
		for (int j = 1; j <= N; ++j) {
			if (MAP[j][i] != 0) {
				MAP[top][i] = MAP[j][i];
				if (top != j) MAP[j][i] = 0;
				top += 1;
			}
		}
	}
}

void left(int MAP[MAXN][MAXN]) {
	rotate_right(MAP);
	up(MAP);
	rotate_left(MAP);
}

void right(int MAP[MAXN][MAXN]) {
	rotate_left(MAP);
	up(MAP);
	rotate_right(MAP);
}
void down(int MAP[MAXN][MAXN]) {
	rotate_right(MAP); rotate_right(MAP);
	up(MAP);
	rotate_right(MAP); rotate_right(MAP);
}

int main() {
	ios_base::sync_with_stdio(false);
	cin >> T;
	for (int i = 1; i <= T; i++) {
		cin >> N >> input;
		for (int j = 1; j <= N; j++) for (int k = 1; k <= N; k++) 
			cin >> MAP[j][k];
		
		if (input[0] == 'r') right(MAP);
		else if (input[0] == 'l') left(MAP);
		else if (input[0] == 'd') down(MAP);
		else if (input[0] == 'u') up(MAP);

		cout << "#" << i << "\n";
		for (int j = 1; j <= N; j++) {
			for (int k = 1; k <= N; k++) {
				cout << MAP[j][k] << " "; 
			}
			cout << "\n";
		}
	}
}
{% endhighlight %}