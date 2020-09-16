---
layout: post
title:  "5678. [Professional] 팰린드롬 "
subtitle: ""
date:   2019-09-27 11:15:08 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXRrK7KhO4DFAUo"}})

## [풀이]

{% highlight c %}
#include <iostream>
#define MAX(a, b) (a > b ? a : b)
using namespace std;
 
char buf[1001];
int dp[1001][1001]; // [s][e] == str[start] ~ str[end] is pelind
 
int _strlen(const char * str) {
    int len = 0;
    while (*str != '\0') {
        str++;
        len++;
    }
    return len;
}
 
void init() {
    for (int i = 0; i < 1001; i++) {
        for (int j = 0; j < 1001; j++) {
            if (i == j) dp[i][j] = 1;
            else if (i - j == 1 || i - j == -1) dp[i][j] = 1;
            else dp[i][j] = 0;
        }
 
    }
}
 
int solve() {
    // a~b -> a+1 ~ b-1 -> a+2 ~ b-2 
    int max = 0;
    int len = _strlen(buf);
    if (len == 1) max = 1;
    else if (len == 2) max = 2;
    for (int i = 2; i <= len; i++) { // i = len
        for(int j = 0 ; j < len - i ; j++){
            if (buf[j] == buf[j + i] && dp[j + 1][j + i - 1]) {
                if (j + 2 == j + i - 1 && buf[j + 1] != buf[j + i - 1])
                    continue;
                dp[j][j + i] = 1;
                max = MAX(i + 1, max);
            }
        }
    }
 
 
    return max;
}
 
int main() {
    int T;
    cin >> T;
 
    int res;
    for (register int i = 1; i <= T; i++) {
        init();
        cin >> buf;
        res = solve();
        cout << "#" << i << " " << res << "\n";
    }
}
{% endhighlight %}