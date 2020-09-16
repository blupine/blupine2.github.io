---
layout: post
title:  "1224. [S/W 문제해결 기본] 6일차 - 계산기3"
subtitle: ""
date:   2019-10-19 12:44:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV14tDX6AFgCFAYD"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <deque>
#include <algorithm>
#define MAXN 200
#define ISNUM(a) (a >= '0' && a <= '9')

using namespace std;
char buf[MAXN];
int len, sol;

enum {};

deque<char> oper;
deque<int> stack;
deque<char> postfix;
const char priority[5] = {'(', '+', '-', '/', '*'};
int get_priority(char c) {
	int i = 0;
	for (i = 0; i < 5; i++)
		if (priority[i] == c) break;
	return i;
}

int calc(int n1, int n2, char op) {
	switch (op) {
	case'+':
		return n1 + n2;
	case '-':
		return n1 - n2;
	case '/':
		return n1 / n2;
	case '*':
		return n1 * n2;
	}
}

void solve() {
	for (int i = 0; i < len; ++i) {
		if (ISNUM(buf[i])) postfix.push_back(buf[i]);
		else if (buf[i] == ')') {
			while (oper.back() != '(') {
				postfix.push_back(oper.back());
				oper.pop_back();
			}
			oper.pop_back();// remove '('
		}
		else if (buf[i] == '(') oper.push_back('(');
		else {
			int p = get_priority(buf[i]);
			while (oper.size() != 0 && get_priority(oper.back()) > p) {
				postfix.push_back(oper.back());
				oper.pop_back();
			}
			oper.push_back(buf[i]);
		}
	}

	while (!oper.empty()) {
		postfix.push_back(oper.back());
		oper.pop_back();
	}

	// calc
	int n1, n2;
	for (int i = 0; i < postfix.size(); ++i) {
		if (ISNUM(postfix[i])) stack.push_back(postfix[i] - '0');
		else {
			n1 = stack.back(); stack.pop_back();
			n2 = stack.back(); stack.pop_back();
			stack.push_back(calc(n1, n2, postfix[i]));
		}
	}

	cout << stack[0] << "\n";
}

int main() {
	for (int i = 1; i <= 10; ++i) {
		oper.clear(); stack.clear(); postfix.clear();
		sol = len = 0;
		cin >> len;
		cin >> buf;
		cout << "#" << i << " ";
		solve();
	}
}
{% endhighlight %}