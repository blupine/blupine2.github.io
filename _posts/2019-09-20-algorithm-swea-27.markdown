---
layout: post
title:  "5603. [Professional] 건초더미 "
subtitle: ""
date:   2019-09-20 16:11:08 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXGEbd6cjMDFAUo"}})

## [풀이]

{% highlight c %}
#include <iostream>
#define MAXN 10001
using namespace std;
 
int T, N, avg;
int sum = 0;
int guncho[MAXN];
int solve() {
    avg = sum / N;
    int count = 0;
    for (int i = 0; i < N; i++) {
        if (guncho[i] < avg) {
            count += avg - guncho[i];
        }
    }
    return count;
}
 
int main() {
    ios_base::sync_with_stdio(false);
    cin >> T;
 
    int sol;
    for (int i = 1; i <= T; i++) {
        cin >> N;
        sum = 0;
        for (int j = 0; j < N; j++) {
            cin >> guncho[j];
            sum += guncho[j];
        }
        sol = solve();
        cout << "#" << i << " " << sol << "\n";
    }
 
}
{% endhighlight %}