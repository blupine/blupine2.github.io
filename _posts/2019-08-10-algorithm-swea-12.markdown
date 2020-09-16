---
layout: post
title:  "2477. [모의 SW 역량테스트] 차량 정비소"
subtitle: ""
date:   2019-08-10 13:11:37 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV6c6bgaIuoDFAXy"}})


## [풀이]

{% highlight c %}
#include <iostream>
#include <deque>
#include <queue>
#include <string.h>

#define DEBUG 0
using namespace std;
int N, M, K, A, B; // N : 접수 창구 수, M : 정비 창구 수, K : 고객 수, A : 고객이 이용한 접수 창구, B: 고객이 이용한 장비 창구
int Jeobsu[2][10], Jeongbi[2][10]; // 사용중일 경우 [1][i] = 고객 번호
class Customer{
public:
	int cnum;
	int time; // 도착 시간
	int JeobsuT;
	int JeongbiT;
	int su; // 이용한 접수 창구 번호
	int bi; // 이용한 정비 창구 번호
	Customer(int n, int t){
		cnum = n;
		time = t;
	}
};

deque<Customer> customers;
void get_input(){
	customers.clear();
	memset(Jeobsu, 0, sizeof(Jeobsu));
	memset(Jeongbi, 0, sizeof(Jeongbi));
	cin >> N >> M >> K >> A >> B;
	for(int i = 1 ; i <= N ; i++)
		cin >> Jeobsu[0][i]; // i 번째 접수 창구가 고장을 접수하는 데 걸리는 시간
	for(int i = 1 ; i <= M ; i++)
		cin >> Jeongbi[0][i]; // i 번째 정비 창구가 고장을 정비하는 데 걸리는 시간
	for(int i = 1 ; i <= K ; i++){
		int t;
		cin >> t;
		customers.push_back(Customer(i, t));
	}
}
int get_available_index(int n, int arr[2][10]){ // n : 창구 수
	for(int i = 1 ; i <= n ; i++)
		if(arr[1][i] == 0)
			return i;
	return -1;
}
int solve(){
	int time = 0;
	int complete = 0;
	deque<int> sol;
	deque<Customer> cus = customers;
	deque<int> waitingA; // store index of wating jeobsu customer
	deque<int> waitingB;

	while(1){
		if(complete == K)
			break;
		while(cus.size() != 0 && cus[0].time <= time){
			waitingA.push_back(cus[0].cnum - 1);
			cus.pop_front();
		}
		for(int i = 1 ; i <= M ; i++){
			if(Jeongbi[1][i] != 0){ // 정비 창구에서 정비중인 고객들을 대상으로
				int index = Jeongbi[1][i] - 1;// 정비중인 고객 index
				if(time >= customers[index].JeongbiT + Jeongbi[0][i]){ // 고객의 정비 시작 시간 + 해당 정비 창구의 소요 시간
					Jeongbi[1][i] = 0;
					complete++;
					if(customers[index].su == A && customers[index].bi == B)
						sol.push_back(customers[index].cnum);
				}
			}
		}
		for(int i = 1 ; i <= N ; i++){
			if(Jeobsu[1][i] != 0){ // 접수 창구에서 접수중인 고객들을 대상으로
				int index = Jeobsu[1][i] - 1;// 접수 고객 index
				if(time >= customers[index].JeobsuT + Jeobsu[0][i]){ // 고객의 정비 시작 시간 + 해당 정비 창구의 소요 시간
					Jeobsu[1][i] = 0;
					waitingB.push_back(index);
				}
			}
		}
		while(waitingB.size()){
			int bi = get_available_index(M, Jeongbi);
			if(bi == -1)
				break;
			else{
				Jeongbi[1][bi] = customers[waitingB[0]].cnum;
				customers[waitingB[0]].JeongbiT = time;
				customers[waitingB[0]].bi = bi;
				waitingB.pop_front();
			}
		}
		while(waitingA.size()){
			int su = get_available_index(N, Jeobsu);
			if(su == -1)
				break;
			else{			
				Jeobsu[1][su] = customers[waitingA[0]].cnum;
				customers[waitingA[0]].JeobsuT = time;
				customers[waitingA[0]].su = su;
				waitingA.pop_front();
			}			
		}
		time++;
	}

	if(sol.size()){
		int res = 0;
		for(int i = 0 ; i < sol.size() ; i++){
			res += sol[i];
		}
		return res;
	}else{
		return -1;
	}
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