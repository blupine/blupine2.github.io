---
layout: post
title:  "1234. [S/W 문제해결 기본] 10일차 - 비밀번호 "
subtitle: ""
date:   2019-10-17 12:55:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV14_DEKAJcCFAYD"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <deque>
#include <algorithm>
using namespace std;
char buf[101];
int len, sol;

void solve() {
	deque<int> sol;
	bool flag = true;
	int cnt = '9';
	while (flag) {
		flag = false;
		for (int i = 0; i < len - 1; i++) {
			if (buf[i] == buf[i + 1]) {
				for (int j = 0; i + 2 + j <= len; j++) {
					buf[i + j] = buf[i + 2 + j];
				}
				len -= 2;
				flag = true;
			}
		}
	}
	for (int i = 0; i < len; i++) {
		if (buf[i] > '9') break;	
		cout << buf[i];
	}
}

int main() {
	for (int i = 1; i <= 10; ++i) {
		sol = len = 0;
		cin >> len;
		cin >> buf;
		
		cout << "#" << i << " ";
		solve();
		cout << "\n";
	}
}
{% endhighlight %}