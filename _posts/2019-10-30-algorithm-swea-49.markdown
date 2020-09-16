---
layout: post
title:  "1232. [S/W 문제해결 기본] 9일차 - 사칙연산"
subtitle: ""
date:   2019-10-30 16:23:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV141J8KAIcCFAYD"}})

## [풀이]

{% highlight c %}
#include <iostream>
#define MAXNODE 1001
using namespace std;

int N, A, L, R, sol;
char C[5];
int buf[MAXNODE * 2][3]; int buf_top;

int add(int n1, int n2) { return n1 + n2; }
int sub(int n1, int n2) { return n1 - n2; }
int mul(int n1, int n2) { return n1 * n2; }
int _div(int n1, int n2) { return n1 / n2; }

int (*oper[5])(int, int) = {add, add, sub, mul, _div};

bool isoperator(char *c) {return !(*c <= '9' && *c >= '0');}

int _atoi(char * str) {
	if (isoperator(str)) {
		if (str[0] == '+') return -1;
		else if (str[0] == '-') return -2;
		else if (str[0] == '*') return -3;
		else return -4;
	}
	else {
		int n = 0;
		while (*str != '\0') {
			n = n * 10 + (*str - '0');
			str++;
		}
		return n;
	}
}

int preorder(int node) {
	if (buf[node][0] > 0)
		return buf[node][0];	
	return oper[buf[node][0] * -1](preorder(buf[node][1]), preorder(buf[node][2]));
}

int main() {
	for (int i = 1; i <= 10; ++i) {
		cin >> N;
		for (int i = 0; i < MAXNODE * 2; i++) buf[i][0] = buf[i][1] = buf[i][2] = 0;
		
		for (int i = 1; i <= N; i++) {
			cin >> A >> C;
			buf[A][0] = _atoi(C);
			if (isoperator(C)) {
				cin >> L >> R;
				buf[A][1] = L;
				buf[A][2] = R;
			}
		}

		sol = preorder(1);
		cout << "#" << i << " " << sol << "\n";
		
	}
}

{% endhighlight %}