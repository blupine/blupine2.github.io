---
layout: post
title:  "[1208. [S/W 문제해결 기본] 1일차 - Flatten"
subtitle: ""
date:   2019-10-07 12:33:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV139KOaABgCFAYh"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <algorithm>
#include <cstring>
using namespace std;

int T, N;
int box[100];
int cnt[1001];
int maxcount;

bool compare(int n, int m) {
	return n > m;
}

// head, tail만 정렬
void _sort() {
	int head = box[0];
	int index = 0;
	while (index < 99 && head < box[index + 1]) {
		box[index] = box[index + 1];
		index += 1;
	}
	box[index] = head;

	head = box[99];
	index = 99;
	while (index > 0 && head > box[index - 1]) {
		box[index] = box[index - 1];
		index -= 1;
	}
	box[index] = head;

}

int main() {
	
	T = 10;
	for (int i = 1; i <= T; ++i) {
		cin >> N; // 덤프 횟수
		
		for (int j = 0; j < 100; ++j) cin >> box[j];
		
		sort(box, box + 100, compare);
		for (int j = 1; j <= N; ++j) {
			
			box[0]--;
			box[99]++;
			_sort();
		}		

		cout << "#" << i << " " << box[0] - box[99] << "\n";
	}

}
{% endhighlight %}