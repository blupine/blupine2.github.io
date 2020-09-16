---
layout: post
title:  "1861. 정사각형 방"
subtitle: ""
date:   2019-11-20 20:58:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV5LtJYKDzsDFAXc"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <deque>

using namespace std;
#define MAXN 1001
int T, N, srow, scol, _max;

int map[MAXN][MAXN];
int visit[MAXN][MAXN];
int maxarr[MAXN][MAXN];

#define ISVALID(r, c) (r >= 1 && r <= N && c >= 1 && c <= N)
const int dir[4][2] = { {-1, 0}, {1, 0}, {0, -1}, {0, 1} };

int dfs(int depth, int row, int col) {

	int nextrow, nextcol, ret = 0, temp;
	bool flag = true;
	for (int i = 0; i < 4; i++) {
		
		nextrow = row + dir[i][0];
		nextcol = col + dir[i][1];

		if (ISVALID(nextrow, nextcol) && map[nextrow][nextcol] == map[row][col] + 1) {
			flag = false;
			if (visit[nextrow][nextcol] != 0) {
				temp = maxarr[nextrow][nextcol] + 1;
			}
			else {
				visit[nextrow][nextcol] = 1;
				temp = dfs(depth + 1, nextrow, nextcol) + 1;
				// 현재 좌표에서 갈 수 있는 최대 거리 반환, 현재 좌표의 최대 값 갱신
				// 최대 값을 반환
			}
			if (temp > ret) {
				ret = temp;
				maxarr[row][col] = ret;
				visit[row][col] = 1;
			}
		}
		else if (i == 3 && flag) { // 방문할 방이 없을 경우
			maxarr[row][col] = 1;
			visit[row][col] = 1;
			return 1;
		}		
	}
	return ret;
}

void solve() {
	int nextrow, nextcol, ret;
	for (int i = 1; i <= N; ++i) {
		for (int j = 1; j <= N; ++j) {
			if (visit[i][j] == 0) {
				//cout << "visiting : " << i << ", " << j << "\n";
				visit[i][j] = 1;
				ret = dfs(1, i, j);
				
				if (ret > _max) {
					_max = ret;
					srow = i; scol = j;
				}
				else if (ret == _max) {
					if (map[i][j] < map[srow][scol]) {
						srow = i; scol = j;
					}
					//srow = map[i][j] < map[srow][scol] ? i : srow;
					//scol = map[i][j] < map[srow][scol] ? j : scol;
				}
				//visit[i][j] = 0;
			}
		}
	}
}


int main() {
	cin >> T;
	for (int i = 1; i <= T; ++i) {
		srow = scol = 0;
		_max = -0x7fffffff;
		cin >> N;
		for (int j = 1; j <= N; ++j) {
			for (int k = 1; k <= N; ++k) {
				cin >> map[j][k];
				visit[j][k] = maxarr[j][k] = 0;
			}
		}

		solve();
		
		cout << "#" << i << " " << map[srow][scol] << " " << maxarr[srow][scol] << "\n";
	}
}
{% endhighlight %}