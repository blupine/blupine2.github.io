---
layout: post
title:  "1218. [S/W 문제해결 기본] 4일차 - 괄호 짝짓기"
subtitle: ""
date:   2019-10-13 12:15:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV14eWb6AAkCFAYD"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <vector>
using namespace std;

int len;
char buf[1001];

int _strlen(char * buf) {
	int len = 0;
	while (*buf++ != '\0') ++len;
	return len;
}


int main() {
	int T = 10;
	for (int i = 1; i <= T; ++i) {
		int cont[4][2] = { {0, 0}, {0, 0}, {0, 0}, {0, 0} };
		vector<char> one[2], two[2], three[2], four[2];
		cin >> len;
		cin >> buf;
		int sol = 1;
		int close = 0, open = 0;
		for (int j = 0; j < _strlen(buf); ++j) {
			switch (buf[j]) {
			case '{':
				open += 1;
				cont[0][0]++;
				break;
			case '[':
				cont[1][0]++;
				open += 1;
				break;
			case '(':
				cont[2][0]++;
				open += 1;
				break;
			case '<':
				cont[3][0]++;
				open += 1;
				break;
			case '}':
				cont[0][1]++;
				close += 1;
				break;
			case ']':
				cont[1][1]++;
				close += 1;
				break;
			case ')':
				cont[2][1]++;
				close += 1;
				break;
			case '>':
				cont[3][1]++;
				close += 1;
				break;
			}
		}
		if (close != open || cont[0][0] != cont[0][1] || cont[1][0] != cont[1][1] || cont[2][0] != cont[2][1] || cont[3][0] != cont[3][1])
			sol = 0;

		cout << "#" << i << " " << sol << endl;
	}



}
{% endhighlight %}