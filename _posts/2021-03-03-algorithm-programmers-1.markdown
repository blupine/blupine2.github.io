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

#### JAVA 풀이
{% highlight java %}
import java.util.HashMap;
import java.util.ArrayList;
import java.util.Collections;
class Solution {
    public int[] solution(String[] info, String[] query) {
        int[] answer = new int[query.length];

        int ansidx = 0;
        HashMap<String, ArrayList<Integer>> hmap = new HashMap<>();
        for(String str : info) {
            String[] arr = str.split(" ");
            for(int i = 0; i < 16; i++) {
                String key = ((i & (1 << 0)) != 0 ? arr[0] : "-") +
                        ((i & (1 << 1)) != 0 ? arr[1] : "-") +
                        ((i & (1 << 2)) != 0 ? arr[2] : "-") +
                        ((i & (1 << 3)) != 0 ? arr[3] : "-");

                ArrayList<Integer> li = hmap.getOrDefault(key, new ArrayList<Integer>());
                li.add(Integer.parseInt(arr[4]));
                hmap.put(key, li);
            }
        }

        for(String key : hmap.keySet()) {
            Collections.sort(hmap.get(key));
        }
        
        for(String ql : query) {
            String[] q = ql.split(" ");
            String key = q[0] + q[2] + q[4] + q[6];
            Integer target = Integer.parseInt(q[7]);

            ArrayList<Integer> li = hmap.getOrDefault(key, new ArrayList<Integer>());
            int idx = binarySearch(li, target);
            answer[ansidx++] = li.size() - idx;
        }
        return answer;
    }
    
    int binarySearch(ArrayList<Integer> list, int target) {
        int start = 0, end = list.size() - 1, mid = 0;
        while(start <= end) {
            mid = (start + end) / 2;
            if(target > list.get(mid)) {
                start = mid + 1;
            }
            else{
                end = mid - 1;
            }
        }
        return start;
    }
}
{% endhighlight %}

#### C++ 풀이
{% highlight c++ %}
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