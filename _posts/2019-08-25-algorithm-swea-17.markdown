---
layout: post
title:  "5648. [모의 SW 역량테스트] 원자 소멸 시뮬레이션"
subtitle: ""
date:   2019-08-25 18:33:13 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXRFInKex8DFAUo"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <deque>
#include <cstring>
#include <algorithm>
#define ABS(i) ((i) > 0 ? (i) : -(i))
using namespace std;
class Atom{
public:
	int x, y;
	int dir;
	int energy;
	Atom(int a, int b, int d, int e){
		x = a; y = b; dir = d ; energy = e;
	}
};
class Collision{
public:
	int atom1, atom2;
	int energy;
	float time;
	Collision(int a, int b, int e, float t){
		atom1 = a; atom2 = b; energy = e; time = t;
	}
};
bool compare(Collision c1, Collision c2){
	return c1.time < c2.time;
}
deque<Atom> atoms;
deque<Collision> collisions;
int N; // 원자수
int dir[4][2] = { {-1, 0}, {1, 0}, {0, -1}, {0, 1} }; // 상0, 하1, 좌2, 우3
int flag[1000];
float isCollide(Atom a1, Atom a2){
	int x_diff = a1.x - a2.x;
	int y_diff = a1.y - a2.y;
	float time = (ABS(x_diff) + ABS(y_diff)) / 2.0;
	if(x_diff == 0 && a1.dir < 2){ // 동일한 x 선상에 있고 위 또는 아래를 향할 때
		if(a1.dir == 0 && a2.dir == 1 && y_diff < 0) // 위를 향하고 a2가 더 위에 있을 때
			return time;
		else if(a1.dir == 1 && a2.dir == 0 && y_diff > 0)
			return time;
	}
	else if(y_diff == 0 && a1.dir >= 2){ // 동일한 y 선상에 있고 좌 또는 우를 향할 때 
		if(a1.dir == 2 && a2.dir == 3 && x_diff > 0)
			return time;
		else if(a1.dir == 3 && a2.dir == 2 && x_diff < 0)
			return time;
	}
	else if(ABS(x_diff) == ABS(y_diff)){
		if(a1.dir == 2 || a1.dir == 3){  // y_diff < 0 : a1이 아래에 있을 때 , x_diff < 0 : a1이 왼쪽에 있을 때
			if((a1.dir == 2 && a2.dir == 1 && x_diff > 0 && y_diff < 0) // a1이 오른쪽 아래일 때
				|| (a1.dir == 2 && a2.dir == 0 && x_diff > 0 && y_diff > 0) // a1이 오른쪽 위일 때)
				|| (a1.dir == 3 && a2.dir == 0 && x_diff < 0 && y_diff > 0) // a1이 왼쪽 위일 때
				|| (a1.dir == 3 && a2.dir == 1 && x_diff < 0 && y_diff < 0)) // a1이 왼쪽 아래일 때
				return time;
		}
		else if(a1.dir == 0 || a1.dir == 1){
			if((a1.dir == 0 && a2.dir == 3 && x_diff > 0 && y_diff < 0) // a1이 오른쪽 아래일 때 
				|| (a1.dir == 0 && a2.dir == 2 && x_diff < 0 && y_diff < 0) // a1이 왼쪽 아래일 때
				|| (a1.dir == 1 && a2.dir == 3 && x_diff > 0 && y_diff > 0) // a1이 오른쪽 위일 때
				|| (a1.dir == 1 && a2.dir == 2 && x_diff < 0 && y_diff > 0))// a1이 왼쪽 위일 때)
				return time;
		}
	}
	return 0.0;	
}
void get_input(){
	atoms.clear();
	collisions.clear();
	memset(flag, 0, sizeof(flag));
	int x, y, d, k;
	cin >> N;
	for(int i = 0 ; i < N ; i++){
		cin >> x >> y >> d >> k; 
		atoms.push_back(Atom(x, y, d, k));
	}
}
int solve(){
	for(int i = 0 ; i < atoms.size(); i++){
		for(int j = 0 ; j < atoms.size(); j++){
			if(i == j) continue;
			float t = isCollide(atoms[i], atoms[j]);
			if(t != 0.0){
				collisions.push_back(Collision(i, j, atoms[i].energy + atoms[j].energy, t));
			}
		}
	}
	int e = 0;
	sort(collisions.begin(), collisions.end(), compare);

	deque<Collision> temp;
	deque<Collision> temp2;	
	while(!collisions.empty()){
		temp.clear();
		temp2.clear();
		float t = collisions[0].time;		
		while(collisions.size() != 0 && collisions.at(0).time == t){
			temp.push_back(collisions[0]);
			collisions.pop_front();
		}
		for(int i = 0 ; i < temp.size() ;i++){
			if(flag[temp[i].atom1] == 0 && flag[temp[i].atom2] == 0){
				temp2.push_back(temp[i]);
			}
		}
		for(int i = 0 ; i < temp2.size(); i++){
			flag[temp2[i].atom1] = 1;
			flag[temp2[i].atom2] = 1;
		}
	}
	for(int i = 0 ; i < N ; i++){
		if(flag[i] == 1)
			e += atoms[i].energy;
	}

	return e;
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