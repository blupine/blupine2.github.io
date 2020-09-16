---
layout: post
title:  "5604. [Professional] 구간 합 "
subtitle: ""
date:   2019-09-22 16:15:08 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXGGNB6cnEDFAUo"}})

## [풀이]

{% highlight c %}
#include <iostream>
 
using namespace std;
 
long long A;
long long B;
 
unsigned long long _pow(unsigned long long base, int exp)
{
    unsigned long long res = 1;
    while (exp)
    {
        if (exp & 1)
            res *= base;
        exp >>= 1;
        base *= base;
    }
 
    return res;
}
 
 
int get_jari(long long num){
    int i = 0;
    while(num / 10 != 0){
        i++;
        num /= 10;
    }
    return i + 1;
}
 
int get_nth_num(long long num, int n){
    // n == 4 -> 1000 -> 1, 뒤에서 n 번째
 
    // 1300의 3번째 자리를 남기려면 -> % 1000 , / 100
 
    int ret = (num % _pow(10, n)) / _pow(10, n - 1); 
 
    return ret;
}
 
long long get_sum_n_to_m(long long n , long long m){
    int ml = m % 10;
    int nl = n % 10;
    long long gap = m / 10 - n / 10;
    long long res = 0;
    if(nl <= ml){
        for(int i = 0 ; i <= 9 ; i++){
            if(i >= nl && i <= ml){
                res += i * (gap + 1);
            }
            else{
                res += i * (gap);
            }
        }
    }
    else{
        for(int i = 0 ; i <= 9 ; i++){
            if(i <= ml || i >= nl){
                res += i * (gap);
            }
            else{
                res += i * (gap - 1);
            }
        }
    }
 
    //     int ret = 0;    
 
    // for(int i = n ; i <= m ; i++){
    //     ret += i % 10;
    //     n += i;
    // }
 
    // cout << res << endl;
    // cout << ret << endl;
    return res;
}
 
long long solve(){
    long long small, large;
    if(A > B)   {small = B; large = A;}
    else        {small = A; large = B;}
     
    int sjari = get_jari(small);
    int ljari = get_jari(large);
 
    long long total = 0;
 
    int flag = 0;
    if(sjari == ljari && get_nth_num(small, sjari) == get_nth_num(large, ljari)){
        flag = 1;
    }
 
    for(int i = ljari ; i >= 1 ; i--){
        int s = get_nth_num(small, i);
        int l = get_nth_num(large, i);
 
 
        if(flag && s == l){
            // large % 1000
            // large % 100
            // large % 10
            total += s * (large % _pow(10, i ) - small % _pow(10, i) + 1);
        }else{
            long long first = s * (_pow(10, i - 1) - (small % _pow(10, i - 1)));
            total += first;
         
        }
 
        // if(s!= 0 && (i == ljari && s != l)){
        // }
        long long  left = small / _pow(10, i - 1) + 1;
        long long right = large / _pow(10, i - 1) - 1;
                 
        if(left <= right){
            long long sec = get_sum_n_to_m(left, right) * _pow(10, i - 1);
            total += sec;
        }
 
        if(flag && s == l){
            // large % 1000
            // large % 100
            // large % 10
            //total += s * (large % _pow(10, i ) - small % _pow(10, i));
        }else{
            long long third = l * (large % _pow(10, i - 1) + 1);
            total += third;
        }
 
    }
    return total;
}
int main(){
    //cout << get_sum_n_to_m(35, 132);
    int T;
    long long sol;
    cin >> T;
    for(int i = 1 ; i <= T ; i++){
        cin >> A >> B;
        sol = solve();
        cout << "#" << i << " " << sol << endl;
    }
}
{% endhighlight %}