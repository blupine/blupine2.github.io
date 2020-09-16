---
layout: post
title:  "1206. [S/W 문제해결 기본] 1일차 - View"
subtitle: ""
date:   2019-10-05 13:07:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV134DPqAA8CFAYh"}})

## [풀이]

{% highlight c %}
#include <iostream>
using namespace std;
int T = 10, len;
int building[1001];

void init_building() { for (int i = 0; i < 1001; ++i) building[i] = 0; }

int solve() {
	int sol = 0, height, lcomp, rcomp, comp;

	for (int i = 3; i < len - 1; ++i) {
		height = building[i];

		lcomp = building[i - 2] > building[i - 1] ? building[i - 2] : building[i - 1];
		rcomp = building[i + 2] > building[i + 1] ? building[i + 2] : building[i + 1];

		comp = lcomp > rcomp ? lcomp : rcomp;


		if (building[i] - comp >= 1)
			sol += building[i] - comp;
		
	}
	return sol;
}

int main() {
	for (int i = 1; i <= T; ++i) {
		init_building();
		cin >> len;
		for (int j = 1; j <= len; ++j) {
			cin >> building[j];
		}
		cout << "#" << i << " " << solve() << "\n";
	}

}
{% endhighlight %}