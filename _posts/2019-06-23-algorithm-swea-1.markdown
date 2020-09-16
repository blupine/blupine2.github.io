---
layout: post
title:  "1952. [모의 SW 역량테스트] 수영장"
subtitle: ""
date:   2019-06-23 17:33:32 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV5PpFQaAQMDFAUq&categoryId=AV5PpFQaAQMDFAUq&categoryType=CODE"}})


## [풀이]

{% highlight c %}
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

#define MIN(a,b) (a < b ? a : b)
int cost[4] = {0,};

int dfs(int month, int m[13]){
	if(month >= 13)
		return 0;
	int fee[3];

	int nextmonth = 0;
	for(int i = month + 1; i < 13; i++)
		if(m[i] > 0) {
			nextmonth = i;
			break;
		}
	
	fee[0] = cost[0] * m[month];
	fee[1] = cost[1];
	if(nextmonth != 0){
		fee[0] += dfs(nextmonth, m);
		fee[1] += dfs(nextmonth, m);
	}
		
	nextmonth = 0;
	for(int i = month+3 ; i < 13; i++)
		if(m[i] > 0) {
			nextmonth = i;
			break;
		}	
	fee[2] = cost[2];
	if(nextmonth != 0)
		fee[2] += dfs(nextmonth, m);
	
	return MIN(fee[2], MIN(fee[0], fee[1]));
}

int main(){
	int months[13] = {-1,};
	vector <int> solution;
	int ntest = 0;
	cin >> ntest;
	for(int i = 0; i < ntest; i++){
		for(int j = 0 ; j < 4 ; j++)
			cin >> cost[j];

		for(int j = 1 ; j <= 12 ; j++){
			cin >> months[j];
		}

		int start;
		for(int j = 1; j <= 12; j++){
			if(months[j] != 0){
				start = j;
				break;
			}
		}

		int res = dfs(start, months);
		if(res > cost[3])
			res = cost[3];

		solution.push_back(res);
	}
	for(int i = 0 ; i < solution.size(); i++){
		cout << "#" << i+1 << " " << solution.at(i) << endl;
	}

}

{% endhighlight %}