---
layout: post
title:  "2819. 격자판의 숫자 이어 붙이기"
subtitle: ""
date:   2019-11-26 16:13:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV7I5fgqEogDFAXB"}})

## [풀이]

{% highlight c %}
#include <iostream>
#define MAXN 5
#define ISVALID(r, c) (r <= 4 && r >= 1 && c <= 4 && c >= 1)
using namespace std;
int T, N, sol;
char MAP[MAXN][MAXN];
int dir[4][2] = { {-1, 0}, {1, 0}, {0, -1}, {0, 1} };

typedef struct Trie {
	bool isleaf;
	Trie * child[10];
};
Trie nodes[50000]; int nmtop;

Trie * _malloc() {
	nodes[nmtop].isleaf = false;
	for (int i = 0; i < 10; ++i) nodes[nmtop].child[i] = 0;
	return &nodes[nmtop++];
}

Trie root;

void insert(Trie * parent, char * str) {
	if (*str == '\0') {
		if (parent->isleaf == false) {
			sol++;
			parent->isleaf = true;
		}
		else if (parent->isleaf == true) return;
	}
	else {
		if (parent->child[*str - '0'] == '\0')
			parent->child[*str - '0'] = _malloc();
		insert(parent->child[*str - '0'], str + 1);
		return;
	}
}

void dfs(int depth, char *str, int row, int col) {
	str[depth] = MAP[row][col];
	if (depth == 6) {
		str[7] = '\0';
		insert(&root, str);
	}
	else {
		int newrow, newcol;
		for (int i = 0; i < 4; i++) {
			newrow = row + dir[i][0];
			newcol = col + dir[i][1];
			if (ISVALID(newrow, newcol)) {
				dfs(depth + 1, str, newrow, newcol);
			}
		}
	}
}

void solve() {
	for (int i = 1; i < MAXN; i++) {
		for (int j = 1; j < MAXN; j++) {
			char str[10];
			dfs(0, str, i, j);
		}
	}
}

int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(NULL); cout.tie(NULL);
	cin >> T;
	for (int i = 1; i <= T; ++i) {
		sol = 0;
		nmtop = 0;
		root.isleaf = false;
		for (int i = 0; i < 10; i++) root.child[i] = '\0';

		for (int j = 1; j <= 4; ++j) {
			for (int k = 1; k <= 4; ++k) {
				cin >> MAP[j][k];
			}
		}
		solve();
		cout << "#" << i << " " << sol << "\n";
	}
}
{% endhighlight %}