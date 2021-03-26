---
layout: post
title:  "[2021 KAKAO BLIND RECRUITMENT] 광고 삽입"
subtitle: ""
date:   2021-03-06 18:25:23 +0900
categories: algorithm
tags : programmers
---
### [문제링크]({{"https://programmers.co.kr/learn/courses/30/lessons/72414"}})

## [풀이]

시간을 모두 초로 변환, 각 초에 몇 명이 시청중인지를 저장할 수 있는 arr 선언(최대 100시간이므로 3600 * 100 만큼의 정수형 배열 필요)

각 초에 광고를 삽입했을 때, 누적 재생 시간을 저장할 수 있는 sum 배열 선언

이후 순회하면서 각 초에 누적 재생 시간을 구함 (sum[i] = sum[i-1] - arr[i-1] + arr[i + adv_length - 1])


#### JAVA 풀이
{% highlight java %}
class Solution {
    int[] play;
    long[] sum;
    public String solution(String play_time, String adv_time, String[] logs) {
        int play_sec = timeToSec(play_time);
        int adv_sec = timeToSec(adv_time);

        play = new int[play_sec];
        sum = new long[play_sec];
        
        for(String log : logs) {
            int start_sec = timeToSec(log.substring(0, 8));
            int end_sec = timeToSec(log.substring(9, 17));
            for(int i = start_sec ; i < end_sec ; i++) {
                play[i] += 1;
            }
        }
        
        for(int j = 0 ; j < adv_sec ; j++) {
            sum[0] += play[j];
        }    
        
        long max = sum[0];
        int max_idx = 0;
        
        for(int i = 1 ; i < play_sec - adv_sec + 1; i++) {
            sum[i] = sum[i - 1] - play[i - 1] + play[i + adv_sec - 1]; 
            if(sum[i] > max) {
                max = sum[i];
                max_idx = i;
            }
        }
        return secToTime(max_idx);
    }
    
    public int timeToSec(String time) {
        int sec = 0;
        sec += Integer.parseInt(time.substring(0, 2)) * 3600;
        sec += Integer.parseInt(time.substring(3, 5)) * 60;
        sec += Integer.parseInt(time.substring(6, 8));
        return sec;
    }
    
    public String secToTime(int sec) {
        String time = "";
        int h = sec / 3600;
        int m = (sec % 3600) / 60;
        int s = (sec % 60);
        
        time += ((h < 10) ? "0" + h : h) + ":";
        time += ((m < 10) ? "0" + m : m) + ":";
        time += (s < 10) ? "0" + s : s;
        return time;
    }
}
{% endhighlight %}


#### C++ 풀이
{% highlight c++ %}

#include <string>
#include <vector>

using namespace std;

int time_to_sec(string time) {
    return stoi(time.substr(0, 2)) * 3600 + stoi(time.substr(3, 2)) * 60 + stoi(time.substr(6, 2));
}

string sec_to_time(int sec) {
    string ret;
    int h = sec / 3600;
    int m = (sec - h * 3600) / 60;
    int c = sec % 60;
    
    ret += ((h < 10) ? "0" + to_string(h) : to_string(h)) + ":";
    ret += ((m < 10) ? "0" + to_string(m) : to_string(m)) + ":";
    ret += ((c < 10) ? "0" + to_string(c) : to_string(c));
    return ret;
}

string solution(string play_time, string adv_time, vector<string> logs) {
    string answer;
    
    if(adv_time >= play_time)
        return answer = "00:00:00";
    
    int adv_length = time_to_sec(adv_time);
    int play_length = time_to_sec(play_time);

    vector<int> arr(360000);
    vector<long> sum(360000);
    
    for(string log : logs) {
        int start = time_to_sec(log.substr(0, 8));
        int end = time_to_sec(log.substr(9, 8));

        for(int i= start ; i < end ; i++) {
            arr[i] += 1;
        }
    }
    

    for(int i = 0 ; i < adv_length ; i++) {
        sum[0] += arr[i];
    }
    long long max = sum[0], max_idx = 0;

    for(int i = 1 ; i < play_length - adv_length + 1; i++) {
        sum[i] = sum[i - 1] - arr[i - 1] + arr[i + adv_length - 1];
        if(sum[i] > max) {
            max = sum[i];
            max_idx = i;
        }

    }
    
    answer = sec_to_time(max_idx);
    return answer;
}

{% endhighlight %}