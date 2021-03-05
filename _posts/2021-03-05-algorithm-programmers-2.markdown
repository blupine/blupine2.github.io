---
layout: post
title:  "[2021 KAKAO BLIND RECRUITMENT] 순위 검색"
subtitle: ""
date:   2021-03-03 19:25:23 +0900
categories: algorithm
tags : programmers
---
### [문제링크]({{"https://programmers.co.kr/learn/courses/30/lessons/72412"}})

## [풀이]

언어, 직군, 경력, 소울푸드 모든 조합으로 map 테이블 생성. score를 관리

query를 위해서 "-"로 만들어질 수 있는 모든 조합도 map으로 생성

{% highlight c %}
#include <string>
#include <vector>
#include <map>
#include <algorithm>
#include <sstream>
using namespace std;

vector<int> solution(vector<string> info, vector<string> query) {
    vector<int> answer;
    map<string, vector<int>> table;
    string key[4], null; int score;     
    
    for(string s : info) {
        stringstream ss(s);
        ss >> key[0] >> key[1] >> key[2] >> key[3] >> score;
        for(int i = 0 ; i < 16 ; i++) {
            string tmpkey;
            for(int j = 0 ; j < 4 ; j++) {
                tmpkey += (i & (1 << j)) ? "-" : key[j];
            }
            table[tmpkey].push_back(score);
        }
    }

    for(auto &m : table) {
        sort(m.second.begin(), m.second.end());
    }
    
    for(string q : query) {
        stringstream ss(q);
        ss >> key[0] >> null >> key[1] >> null >> key[2] >> null >> key[3] >> score;
        vector<int> scores = table[key[0] + key[1] + key[2] + key[3]];
        answer.push_back(scores.end() - lower_bound(scores.begin(), scores.end(), score));
    }
    
    return answer;
}
{% endhighlight %}