---
layout: post
title:  "[2021 KAKAO BLIND RECRUITMENT] 메뉴 리뉴얼"
subtitle: ""
date:   2021-03-13 22:05:16 +0900
categories: algorithm
tags : programmers
---
### [문제링크]({{"https://programmers.co.kr/learn/courses/30/lessons/72411"}})

## [풀이]

각 Course에 들어온 개수만큼 Order의 음식을 조합(count 메소드), 조합된 문자열을 정렬(AB == BA이므로 정렬해서 처리)해서 hmap에 넣어둠

이후 각 Course에서 모든 조합을 다 찾은 다음에, PQ에 가장 많이 주문받은 조합을 뽑음


{% highlight java %}
import java.util.Arrays;
import java.util.HashMap;
import java.util.PriorityQueue;
class Solution {

    static HashMap<String, Integer> hmap;
    static int max;
    public String[] solution(String[] orders, int[] course) {
        PriorityQueue<String> pq = new PriorityQueue<>();
        for(int i = 0; i < course.length; i++) {
            hmap = new HashMap<>();  // 각 course마다 카운트 하기 위해 hashmap 초기화
            max = 0;
            for(int j = 0; j < orders.length; j++) {
                count(orders[j], "", 0, course[i]);
            }
            
            for(String key : hmap.keySet()) {
                if(max > 1 && hmap.get(key) == max) { // max가 같은 조합이 여러개 있을 경우
                    pq.offer(key);
                }
            }
        }
        
        String[] answer = new String[pq.size()];
        int idx = 0;
        while(!pq.isEmpty()) {
            answer[idx++] = pq.poll();
        }
        return answer;
    }
    
    public void count(String order, String combi, int cur, int len) {
        if(combi.length() == len) {
            char[] charArr = combi.toCharArray();
            Arrays.sort(charArr);
            String sorted = String.valueOf(charArr);
            hmap.put(sorted, hmap.getOrDefault(sorted, 0) + 1); // 이미 hashmap에 있을 경우 + 1, 없을 경우 초기값 1 설정
            max = Math.max(max, hmap.get(sorted));
        }
        else {  
            for(int i = cur ; i < order.length() ; i++) {
                count(order, combi + order.charAt(i), i+1, len);
            }
        } 
    }
}
{% endhighlight %}