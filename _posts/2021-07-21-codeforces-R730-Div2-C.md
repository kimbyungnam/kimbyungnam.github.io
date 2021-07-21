---
layout: post
title: "Need for Pink Slips (#C)"
subtitle: "Codeforces Round 730 Div.2"
categories: algorithm
tags: codeforces
---

## 문제파악

경기를 끝날 때 까지 이길 수 있는 상황에서, 승리 시 각 쪽지를 뽑을 확률이 존재하는 x 개의 쪽지 중 1 개를 뽑을 기회가 주어지며  경기는 "pink slip"이 나올때 까지 진행될 때 pink slip을 뽑는 경기의 수의 기댓값을 계산해야 한다

> 승리 후 어떤 쪽지를 뽑는 것에 따라 모든 쪽지에 확률의 변동이 있다

## IDEA

1. x 가 3으로 시작해서 경기가 진행될 수록 감소만이 가능하다
2. "pink slip"을 뽑을 확류을 감소하지 않고 증가만 가능하다
3. 문제 제약사항에 따라 v는 최소 0.1이며 두 번째 연산의 경우 $v/2$만큼 증가하게 된다
4. 모든 경우를 탐색할 경우 최대 가짓수는 $2^{1/0.05} = 2^{20}$​


## CODE

```c++
#include <stdio.h>
#include <math.h>
double draw(double* percent, double V, int* flag){
    double result = 0;
    if(*percent < V || fabs(*percent-V) <= __FLT_EPSILON__){
        result = *percent;
        *percent = 0;
        *flag = 0;
    }
    else{
        result = V;
        *percent -= V;
    }
    return result;
}
 
double dfs(double C, int flag_C, double M, int flag_M, double P, double V, int count, double percent){
    double result = 0, m;
 
    // draw pink slip
    result += count * P *percent;
 
    double dup_C = C;
    double dup_M = M;
    double dup_P = P;
    int dup_flag_C = flag_C;
    int dup_flag_M = flag_M;
 
    // draw C
    if(flag_C){
        m = draw(&C, V, &flag_C);
        if(flag_M){
            M += m/2;
            P += m/2;
        }
        else{
            P += m;
        }
        result += dfs(C, flag_C, M, flag_M, P, V, count+1, percent*dup_C);
        C = dup_C;
        M = dup_M;
        P = dup_P;
    }
 
    flag_C = dup_flag_C;
 
    // draw M
    if(flag_M){
        m = draw(&M, V, &flag_M);
        if(flag_C){
            C += m/2;
            P += m/2;
        }
        else{
            P += m;
        }
        result += dfs(C, flag_C, M, flag_M, P, V, count+1, percent*dup_M);
        C = dup_C;
        M = dup_M;
        P = dup_P;
    }
 
    return result;
}
 
int main(){
    int T;
    double C, M, P, V;
    scanf("%d", &T);
 
    for(int  t= 1; t<T+1; t++){
        scanf("%lf %lf %lf %lf", &C, &M, &P, &V);
 
        printf("%.12lf\n", dfs(C, 1, M, 1, P, V, 1, 1));
    }
}
```



## 주의 사항

확률을 다루다 보니 float 타입 변수 비교시 발생하는 문제점들을 고려해야 한다. math.h 헤더에 있는 fabs함수와 __FLT_EPSILON__를 활용했다.

전체 탐색을 하여도 해결할 수 있는 문제라는 것을 파악하는 것이 중요하다. 이를 위해서 시간 복잡도를 계산하는 것이 중요하다.