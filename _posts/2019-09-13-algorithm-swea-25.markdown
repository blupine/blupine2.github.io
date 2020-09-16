---
layout: post
title:  "7829. 보물왕 태혁 "
subtitle: ""
date:   2019-09-13 15:23:08 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWtInr3auH0DFASy"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <algorithm>
#include <vector>
#include <numeric>

using namespace std;

int gcd(int a, int b) {
    return (a > 0) ? gcd(b % a, a) : b;
}

int lcm(int a, int b) {
    return a * b / gcd(a, b);
}

int solution(vector<int> &v) {
    auto lambda = [](int &lElem, int &rElem) {return lcm(lElem, rElem); };
    return accumulate(next(v.begin()), v.end(), v[0], lambda);
}
int main(){
    int tc;
    cin >> tc;

    for (int t = 1; t <= tc; t++) {
        int p;
        cin >> p;

        vector<int> v;

        int tNum;
        while (p--) {
            cin >> tNum;
            v.push_back(tNum);
        }

        auto min_max = minmax_element(v.begin(), v.end());
        cout << "#" << t << " " << (*min_max.first) * (*min_max.second) << endl;

    }
}
{% endhighlight %}