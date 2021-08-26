---
layout: post
title:  "[2019 KAKAO BLIND RECRUITMENT] 매칭 점수"
subtitle: ""
date:   2021-08-24 17:37:55 +0900
categories: algorithm
tags : programmers
---

### [문제링크]({{"https://programmers.co.kr/learn/courses/30/lessons/42893"}})

## [풀이]

1. 한 웹페이지의 url은 HTML의 <head> 태그 내에 <meta> 태그의 값으로 주어진다.
   1. 정확히는 "<meta property="og:url"" 형식을 따른다. -> 문제에 정확한 조건이 없다..

```c++
#include <string>
#include <algorithm>
#include <vector>
#include <iostream>
#include <map>
#define IsChar(a) ((a >= 'a' && a <= 'z') || (a >= 'A' && a <= 'Z'))
using namespace std;

typedef struct Page {
    int index;
    string page, lower_page;
    string url;
    double basic_score, ex_link, link_score, matching;
    bool operator<(const Page &cmp) const {
        if(matching != cmp.matching)
            return matching > cmp.matching;
        return index < cmp.index;
    }
}Page;

string tolower(string page) {
    for(int i = 0 ; i < page.size() ; i++) {
        if(page[i] >= 'A' && page[i] <= 'Z') 
            page[i] = page[i] - ('A' - 'a');
    }
    return page;
}

string geturl(string page) {
    int head_pos = page.find("<head");
    int meta_pos = page.find("<meta property=\"og:url\"", head_pos);
    int url = page.find("\"https", meta_pos) + 1;
    int end_pos = page.find("\"", url + 1);
    return page.substr(url, end_pos - url);
}

int get_basic_score(string lower, string word) {
    string loword = tolower(word);
    int pos = 0, cnt = 0;
    while((pos = lower.find(loword, pos)) != string::npos) {
        if(pos == 0) {
            if(pos + loword.size() < lower.size()) {
                if(!IsChar(lower[pos + loword.size()]))
                    cnt++;
            }
        }else if(pos == lower.size() - loword.size() - 1) { // 문장의 마지막에서 찾은 경우
            if(pos >= 1 && !IsChar(lower[pos - 1])) {
                cnt++;
            }
        }else if(!IsChar(lower[pos - 1]) && !IsChar(lower[pos + loword.size()])){
            cnt++;
        }
        pos += 1;
    }
    return cnt;
}

vector<string> get_ex_links(string page) {
    vector<string> ret;
    int pos = 0;
    string token = "<a href=\"";
    while((pos = page.find(token, pos)) != string::npos) {
        int end = page.find("\"", pos + token.size());
        string tmp = page.substr(pos + token.size(), end - (pos + token.size()));
        ret.push_back(tmp);
        pos += 1;
    }
    return ret;
}

int solution(string word, vector<string> pages) {
    int answer = 0;
    map<string, Page> hmap;
    
    for(int i = 0 ; i < pages.size() ; i++) {
        Page tmp;
        tmp.index = i;
        tmp.page = pages[i];
        tmp.lower_page = tolower(pages[i]);
        tmp.basic_score = get_basic_score(tmp.lower_page, word);
        tmp.url = geturl(pages[i]);
        tmp.ex_link = tmp.matching = tmp.link_score = 0;
        hmap[tmp.url] = tmp;
        
    }
    
    for(auto iter = hmap.begin() ; iter != hmap.end() ; iter++) {
        vector<string> vec = get_ex_links(iter->second.page);
        iter->second.ex_link = vec.size();
        for(string link : vec) {
            if(hmap.find(link) != hmap.end()) {
                double score = iter->second.basic_score / vec.size(); 
                hmap[link].link_score += score;
            }

        }
    }
    
    auto ans = hmap.begin();
    for(auto iter = hmap.begin() ; iter != hmap.end() ; iter++) {
        iter->second.matching = iter->second.basic_score + iter->second.link_score;
    }
    vector<Page> vec;
    for(auto iter = hmap.begin() ; iter != hmap.end() ; iter++) {
        vec.push_back(iter->second);
    }
    
    sort(vec.begin(), vec.end());

    return vec[0].index;
}
```