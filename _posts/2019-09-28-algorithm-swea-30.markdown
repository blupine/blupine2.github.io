---
layout: post
title:  "1231. [S/W 문제해결 기본] 9일차 - 중위순회"
subtitle: ""
date:   2019-09-28 13:37:08 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV140YnqAIECFAYD"}})

## [풀이]

{% highlight c %}
#include<iostream>
#include<cstring>
using namespace std;

char arr[101] = { 0 };

void in_order(int n) {
    if (arr[n] == 0) 
        return;
    in_order(n * 2);
    cout << arr[n];
    in_order(n * 2 + 1);
}

int main() {
    int n;
    for (int t = 1; t <= 10; t++) {
        for(int i = 0 ; i < 101 ; i++) arr[i] = 0;
        cin >> n;
        for (int i = 1; i <= n; i++) {
            int node;
            char v;
            cin >> node >> v;
            if (n % 2 == 0 && n / 2 > i || n % 2 == 1 && n / 2 >= i) {
                scanf("%*d %*d");
            }
            else if (n % 2 == 0 && n / 2 == i) {
                scanf("%*d");
            }
            arr[node] = v;
        }
        cout << "#" << t << ' ';
        in_order(1);
        cout << '\n';
    }
}
{% endhighlight %}