---
layout: post
title:  "1231. [S/W 문제해결 기본] 9일차 - 중위순회"
subtitle: ""
date:   2019-10-21 13:18:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV140YnqAIECFAYD"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <deque>
using namespace std;

char buf[101]; int buf_top;
char input[20];
int N, M, L, R;
char C;

// left child = n * 2, right child = n * 2 + 1

// left 없으면 자기 출력, right 있으면 right로 재귀 
void preorder(int n) {
	if (buf[n] != NULL) {
		preorder(n * 2);
		cout << buf[n];
		preorder(n * 2 + 1);
	}
}

int main() {
	for (int i = 1; i <= 10; i++) {
		for (int i = 0; i < 101; i++) buf[i] = 0;
		buf_top = 0;
		cin >> N;
		cin.getline(input, 20, '\n'); //flushing

		for (int j = 1; j <= N; j++) {
			cin.getline(input, 20, '\n');
			C = input[1] == ' ' ? input[2] : input[3];
			buf[++buf_top] = C;
		}
		cout << "#" << i << " ";
		preorder(1);
		cout << "\n";
	}
}

{% endhighlight %}