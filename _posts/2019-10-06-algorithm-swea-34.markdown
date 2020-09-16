---
layout: post
title:  "1204. [S/W 문제해결 기본] 1일차 - 최빈수 구하기"
subtitle: ""
date:   2019-10-06 12:11:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV13zo1KAAACFAYh"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <cstring>
using namespace std;

int T, N;
int arr[1001];
int cnt[1001];
int maxcount;
int main() {
	cin >> T;

	for (int i = 1; i <= T; ++i) {
		cin >> N;
		maxcount = 0;
		memset(cnt, 0, sizeof(cnt));
		for (int j = 1; j <= 1000; ++j) {
			cin >> arr[j];
			cnt[arr[j]]++;
			if (cnt[arr[j]] > cnt[maxcount]) maxcount = arr[j];
			else if (cnt[arr[j]] == cnt[maxcount]) maxcount = arr[j] > maxcount ? arr[j] : maxcount;
		}

		cout << "#" << i << " " << maxcount << "\n";
	}

}
{% endhighlight %}