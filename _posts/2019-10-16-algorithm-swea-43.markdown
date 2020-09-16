---
layout: post
title:  "1267. [S/W 문제해결 응용] 10일차 - 작업순서"
subtitle: ""
date:   2019-10-16 13:11:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV18TrIqIwUCFAZN"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <deque>
using namespace std;
#define MAXN 1001

int visit[MAXN];
int indegree[MAXN];
deque<int> node[MAXN];
deque<int> sol;
int T, V, E, A, B;

int main() {
	for (int i = 1; i <= 10; i++) {
		cin >> V >> E;
		for (int i = 0; i <= V; i++) {
			node[i].clear();
			indegree[i] = 0;
			visit[i] = 0;
		}
		sol.clear();
		
		for (int j = 0; j < E; j++) {
			cin >> A >> B;
			indegree[B]++;
			node[A].push_back(B);
		}
		while (sol.size() != V) {
			
			int min_indegree = 0x7fffffff, index = 0;
			
			for (int i = 1; i <= V; i++) {
				if (indegree[i] == 0 && visit[i] == 0) {
					min_indegree = indegree[i];
					index = i;
					break;
				}
			}
			// index가 가리키는 노드들에서 indgree 감소해야함
			// index visit 처리하고, index
			

			for (int i = 0; i < node[index].size(); i++) {
				indegree[node[index][i]]--;
			}
			visit[index] = 1;
			sol.push_back(index);
		}


		cout << "#" << i << " ";
		for (int i = 0; i < sol.size(); i++) cout << sol[i] << " ";
		cout << "\n";
	}
	return 0;
}
{% endhighlight %}