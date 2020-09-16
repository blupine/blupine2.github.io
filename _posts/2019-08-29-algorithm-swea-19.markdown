---
layout: post
title:  "5644. [모의 SW 역량테스트] 무선 충전"
subtitle: ""
date:   2019-08-29 18:37:44 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXRDL1aeugDFAUo"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
#define ABS(x) ((x) > 0 ? (x) : -(x))
#define DISTANCE(x, y, a, b) (ABS((x)-(a)) + ABS((y)-(b)))
int M, A;
int dir[5][2] = { {0, 0}, {-1, 0}, {0, 1}, {1, 0}, {0, -1} }; // 상, 우, 하, 좌
int movement[2][101];
int apinfo[8][4]; // row, col, coverage, power
void get_input(){
	movement[0][0] = 0; movement[1][0] = 0;
	cin >> M >> A;
	for(int i = 1 ; i <= M ; i++) cin >> movement[0][i];
	for(int i = 1 ; i <= M ; i++) cin >> movement[1][i];
	for(int i = 0 ; i < A  ; i++) cin >> apinfo[i][1] >> apinfo[i][0] >> apinfo[i][2] >> apinfo[i][3];
}
bool compare(const int &index1, const int &index2){
	return apinfo[index1][3] > apinfo[index2][3];
}

int solve(){
	int pos[2][2] = { {1, 1}, {10, 10} };
	int Apower = 0;
	int Bpower = 0;
	vector<int> Acango, Bcango;

	for(int i = 0 ; i <= M ; i++){
		Acango.clear(); Bcango.clear();
		//get next position
		pos[0][0] += dir[movement[0][i]][0];pos[0][1] += dir[movement[0][i]][1];
		pos[1][0] += dir[movement[1][i]][0];pos[1][1] += dir[movement[1][i]][1];
		for(int j = 0 ; j < A ; j++){
			int Adistance = DISTANCE(pos[0][0], pos[0][1], apinfo[j][0], apinfo[j][1]);
			int Bdistance = DISTANCE(pos[1][0], pos[1][1], apinfo[j][0], apinfo[j][1]);
			if(Adistance <= apinfo[j][2])
				Acango.push_back(j);
			
			if(Bdistance <= apinfo[j][2])
				Bcango.push_back(j);
			
		}
		int Achoose = -1;
		int Bchoose = -1;
		sort(Acango.begin(), Acango.end(), compare); // 파워가 큰 ap 순서대로 정렬
		sort(Bcango.begin(), Bcango.end(), compare);
		
		if(Acango.size() != 0 && Bcango.size() != 0){ // 둘 다 한 개 이상일 때
			if((Acango.size() == 1 && Bcango.size() == 1) || Acango[0] != Bcango[0]) {
				Achoose = Acango[0]; Bchoose = Bcango[0];
			}
			else{ // Acango.size() >= 1 && Bcango.size() >= 1 && Acango[0] == Bcango[0]
				if(Acango.size() == 1){ // Bcango.size() >= 2
					Achoose = Acango[0];
					Bchoose = Bcango[0] == Acango[0] ? Bcango[1] : Bcango[0];
				}else if(Bcango.size() == 1){
					Bchoose = Bcango[0];
					Achoose = Acango[0] == Bcango[0] ? Acango[1] : Acango[0];
				}
				else{
					Achoose = apinfo[Acango[1]][3] < apinfo[Bcango[1]][3] ? Acango[0] : Acango[1];
					Bchoose = Achoose == Acango[1] ? Bcango[0] : Bcango[1];				}
			}

		}else if(Acango.size() == 0 && Bcango.size() == 0){
			continue;
		}else if(Acango.size() == 0){ // Bcango.size() != 0
			Bchoose = Bcango[0];
		}else if(Bcango.size() == 0){ // Acango.size() != 0
			Achoose = Acango[0];
		}

		if(Achoose != -1 && Achoose == Bchoose){
			Apower += apinfo[Achoose][3] / 2;
			Bpower += apinfo[Bchoose][3] / 2;
		}
		else{
			if(Achoose != -1)
				Apower += apinfo[Achoose][3];
			if(Bchoose != -1)
				Bpower += apinfo[Bchoose][3];
			
		}
	}
	return Apower + Bpower;
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