---
layout: post
title:  "5653. [모의 SW 역량테스트] 줄기세포배양"
subtitle: ""
date:   2019-09-04 17:22:08 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXRJ8EKe48DFAUo"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <vector>
#include <algorithm>
#include <cstring>
#define DEBUG 0
#define START_ROW 310
#define START_COL 310
#define MAXMAP 700
using namespace std;
int dir[4][2] = { {-1, 0}, {1, 0}, {0, -1}, {0, 1} }; // 상 하 좌 우
class Cell{
public:
	int row, col, energy;
	int remain; // 활성화되기까지 걸리는 시간
	Cell(int r, int c, int e){
		row = r; col = c ; energy = e; remain = e;
	}
};
bool compare(Cell c1, Cell c2){
	return c1.energy > c2.energy;
}
int N, M, K;
int map[MAXMAP][MAXMAP];
vector<Cell> inactive;
vector<Cell> active;
void get_input(){
	inactive.clear();
	active.clear();
	memset(map, 0, sizeof(map));
	cin >> N >> M >> K;
	for(int i = START_ROW ; i < START_ROW + N ; i++){
		for(int j = START_COL ; j < START_COL + M ; j++){
			cin >> map[i][j];
			if(map[i][j] != 0)
				inactive.push_back(Cell(i, j, map[i][j]));
		}
	}
}
int solve(){
	vector<Cell>::iterator iter;
	vector<Cell> temp;
	for(int i = 0 ; i <= K ;i++){
		temp.clear();
		sort(active.begin(), active.end(), compare);

		for(int j = 0 ; j < active.size() ; j++){
			if(active[j].remain != 0){
				active[j].remain--;
				if(active[j].remain != 0)
					temp.push_back(active[j]);
				int row = active[j].row;
				int col = active[j].col;
				for(int k = 0 ; k < 4 ; k++){
					int newrow = row + dir[k][0];
					int newcol = col + dir[k][1];
					if(map[newrow][newcol] == 0){

						map[newrow][newcol] = active[j].energy;
						inactive.push_back(Cell(newrow, newcol, active[j].energy));
					}
				}
			}
		}
		active = temp;
		temp.clear();
		if(i != K){ // 마지막 시간에는 inactive를 active로 변경 ㄴㄴ
			for(int j = 0 ; j < inactive.size(); j++){
				if(inactive[j].remain == 0){
					inactive[j].remain = inactive[j].energy;
					active.push_back(inactive[j]);				
				}
				else{
					inactive[j].remain--;
					temp.push_back(inactive[j]);
				}
			}
			inactive = temp;
		}		
	}
	return active.size() + inactive.size();
}
int main(){
	int sol[50] = {0,};
	int ntest;
	cin >> ntest;
	for(int i = 0 ; i < ntest ; i++){
		get_input();
		sol[i] = solve();
	}
	for(int i = 0 ; i < ntest ; i++)
		cout << "#" << i+1 << " " << sol[i] << endl;
}
{% endhighlight %}