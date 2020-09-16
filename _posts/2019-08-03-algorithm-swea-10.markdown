---
layout: post
title:  "2383. [모의 SW 역량테스트] 점심 식사시간"
subtitle: ""
date:   2019-08-03 17:03:37 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV5-BEE6AK0DFAVl"}})


## [풀이]

{% highlight c %}
#include <iostream>
#include <deque>
#include <utility>
#include <algorithm>
#include <string.h>

#define DISTANCE(posA, posB) (abs(posA.first - posB.first) + abs(posA.second - posB.second))
#define MAXMAP 12
using namespace std;
typedef pair<int, int> pos;
int map[MAXMAP][MAXMAP] = {-1,};
int N;
int minimum;
class Stair{
public:
	int row;
	int col;
	int length;
	Stair(int r, int c){
		row = r;
		col = c;
		length = map[r][c];
	}
};
deque<pos> persons;
deque<Stair> stairs;
int check(int stair_num, deque<int> deq){
	for(int i = 0 ; i < deq.size(); i++)
		if(deq[i] != stairs[stair_num-1].length * -1 - 1)
			return 0;
	return 1;
}
int count_onstair(int stair_num, deque<int> deq){ // stair num must be 1 or 2
	int ret = 0;
	for(int i = 0 ; i < deq.size(); i++)
		if(deq[i] < 0 && deq[i] > stairs[stair_num-1].length * -1 - 1) 
			ret++;
	return ret;
}
void get_input(){
	minimum = 99999;
	persons.clear();
	stairs.clear();
	memset(map, -1, sizeof(map));
	cin >> N;
	for(int i = 1 ; i <= N ; i++){
		for(int j = 1 ; j <= N ; j++){
			cin >> map[i][j];
			if(map[i][j] == 1){
				persons.push_back(pos(i, j));
			}
			else if(map[i][j] > 1){
				stairs.push_back(Stair(i, j));
			}
		}
	}
}
int dfs(int depth, deque<int> stair1, deque<int> stair2){
	if(depth == persons.size()){
		int time = 0;
		deque<int> distance1;
		deque<int> distance2;
		for(int i = 0 ; i < stair1.size() ; i++)
			distance1.push_back(DISTANCE(persons[stair1[i]-1], pos(stairs[0].row, stairs[0].col)));
		for(int i = 0 ; i < stair2.size() ; i++)
			distance2.push_back(DISTANCE(persons[stair2[i]-1], pos(stairs[1].row, stairs[1].col)));

		while(1){
			if(check(1, distance1) && check(2, distance2)) {
				if(time < minimum)
					minimum = time;
				return time;
			}

			time++;
			if(time > minimum)
				return 99999;			
			for(int i = 0 ; i < distance1.size(); i++)
				if(distance1[i] < 0 && distance1[i] > stairs[0].length * -1 -1)
					distance1[i]--;

			for(int i = 0 ; i < distance2.size(); i++)
				if(distance2[i] < 0 && distance2[i] > stairs[1].length * -1 -1)
					distance2[i]--;

			for(int i = 0 ; i < distance1.size(); i++){
				int lock1 = count_onstair(1, distance1);	
				if(distance1[i] == 0 && lock1 >= 3){}
				else if(distance1[i] >= 0)
					distance1[i]--;
			}
			for(int i = 0 ; i < distance2.size(); i++){
				int lock2 = count_onstair(2, distance2);
				if(distance2[i] == 0 && lock2 >= 3){}
				else if(distance2[i] >= 0)
					distance2[i]--;
			}
		}

	}else{
		deque<int> deq1 = stair1;
		deque<int> deq2 = stair2;
		deq1.push_back(depth+1);
		deq2.push_back(depth+1);

		int ret1 = dfs(depth + 1, deq1, stair2);
		int ret2 = dfs(depth + 1, stair1, deq2);
		return min(ret1, ret2);
	}
}
int solve(){
	deque<int> null1, null2;
	return dfs(0, null1, null2);
}

int main(){
     ios::sync_with_stdio(false);
	int sol[50] = {0,};
	int ntest;
	cin >> ntest;
	for(int i = 0 ; i < ntest ; i++){
		get_input();
		sol[i] = solve();
	}
	for(int i = 0 ; i <ntest ; i++)
		cout << "#" << i+1 << " " << sol[i] << endl;
}
{% endhighlight %}