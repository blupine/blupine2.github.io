---
layout: post
title:  "1219. [S/W 문제해결 기본] 4일차 - 길찾기"
subtitle: ""
date:   2019-09-30 16:27:08 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV14geLqABQCFAYD"}})

## [풀이]

{% highlight c %}
#include<iostream>
#include<string.h>
using namespace std;
int arr[100][2] = { 0, };

int dfs(int n,int way){
    if (arr[n][way] == 99)return 1;
    if (arr[n][way] == 0) return 0;

    if (dfs(arr[n][way], 0)) return 1;//첫번째 배열의 순서쌍
    return dfs(arr[n][way], 1);//두번째 배열의 순서쌍
}

int main() {
    for (int t = 1; t <= 10; t++) {
        int seq;
        int n;
        cin >> seq >> n;//테케번호와 길의 갯수 입력
        for (int i = 0; i < n; i++) {//순서쌍 입력
            int a, b;
            cin >> a >> b;
            if (arr[a][0] != 0) {
                arr[a][1] = b;
                continue;
            }
            arr[a][0] = b;
        }
        cout << "#" << seq << ' ' << dfs(0, 0) << endl;
        memset(arr, 0, sizeof(arr));
    }
    return 0;
}
{% endhighlight %}