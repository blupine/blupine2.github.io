---
layout: post
title:  "2382. [모의 SW 역량테스트] 미생물 격리"
subtitle: ""
date:   2019-07-29 19:23:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV597vbqAH0DFAVl"}})


## [풀이]

세개가 동시에 한칸에 모이는 케이스에 주의해서 풀어야 한다..

{% highlight c %}
#include <iostream>
#include <deque>
#include <utility>
#include <algorithm>
#include <string.h>

#define MAXMAP 100
#define OPOSITE(i)	(i % 2 == 1 ? i + 1 : i - 1)
using namespace std;
typedef pair<int, int> pos;

pair<int, int> map[MAXMAP][MAXMAP]; // <dir, num>
int N, M, K;
int dir[5][2] = { {}, {-1, 0}, {1, 0}, {0, -1}, {0, 1} }; // (상: 1, 하: 2, 좌: 3, 우: 4)

class Microbe{
public:
	int row;
	int col;
	int num;
	int direction; // (상: 1, 하: 2, 좌: 3, 우: 4)

	Microbe(int r, int c, int number, int direct){
		row = r;
		col = c;
		num = number;
		direction = direct;
	}
	pos move_next(){ // return next position
		int newrow = row + dir[direction][0];
		int newcol = col + dir[direction][1];
		// 만약 벽에 닿았을 경우
		if(newrow == 0 || newrow == N - 1 || newcol == 0 || newcol == N - 1){
	
			direction = OPOSITE(direction);
			num = num / 2;	
		}
		row = newrow; col = newcol;
		return pos(row, col);
	}
};

deque<Microbe> miseng;
deque<Microbe> nextstep;

void get_input(){
	miseng.clear();
	nextstep.clear();
	int row, col, num, dir;
	memset(map, 0, sizeof(map));
	cin >> N >> M >> K;// N : 크기,  M : 격리 시간 , K : 미생물 군집 개수
	for(int i = 0 ; i < K ; i++){
		cin >> row >> col >> num >> dir;
		miseng.push_back(Microbe(row, col, num, dir));
	}
}
bool compare(Microbe s1, Microbe s2){
	return s1.num > s2.num;
}
int solve(){
	for(int i = 0 ; i < M ; i++){ // 각 격리 시간에 대해서
		sort(miseng.begin(), miseng.end(), compare);

		for(int j = 0 ; j < miseng.size(); j++){
			pos newpos = miseng.at(j).move_next();
			if(miseng.at(j).num != 0){ // 미생물이 소멸되지 않았으면 새로 좌표에 기록
				if(map[newpos.first][newpos.second] != make_pair(0, 0)){
					int val = map[newpos.first][newpos.second].second;
					int direc = val > miseng.at(j).num ? map[newpos.first][newpos.second].first : miseng.at(j).direction;
					int newval = val + miseng.at(j).num;
					map[newpos.first][newpos.second] = make_pair(direc, newval); 
				}
				else{
					map[newpos.first][newpos.second] = make_pair(miseng.at(j).direction, miseng.at(j).num); 
				}
			}
		}
		// read map & make new miseng deque
		for(int j = 0 ; j < N ; j++){
			for(int k = 0 ; k < N ; k++){
				if(map[j][k] != make_pair(0, 0)){
					nextstep.push_back(Microbe(j, k, map[j][k].second, map[j][k].first));
				}
			}
		}
		memset(map, 0, sizeof(map));
		miseng = nextstep;
		nextstep.clear();
	}
	int res = 0;
	for(int i = 0 ; i < miseng.size(); i++){
		res += miseng.at(i).num;
	}
	return res;
}

int main(){
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