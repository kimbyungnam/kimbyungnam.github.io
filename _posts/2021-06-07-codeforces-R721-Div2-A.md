---
layout: post
title: "And Then There Were K (#A)"
subtitle: "Codeforces Round 721 Div.2"
categories: algorithm
tags: codeforces
---

## 문제파악

n이라는 숫자가 주어졌을 때 1씩 줄어든 값을 계속 AND 연산을 수행 했을 때 0이 되는 순간에 연산되는 K 값을 찾는 문제입니다.



## IDEA

1. 반복된 AND 연산의 결과값에서 특정 비트 위치의 값이 0이 되기 위해서는 피연산자들 중 **단 1개라도 해당 위치에 0 값**이 오면 된다
2. 1씩 값을 감소시키다 보면 **MSB의 값이 제일 마지막에 변경된다**
3. 즉, MSB 위치의 값이 0으로 바뀌는 그 순간의 값($2^{MSB}-1$)이 바로 K가 됨을 알 수 있다



## CODE

```c++
#include <stdio.h>

#define MAXN (1000000000)
#define MAXT (30000)

/*
* MSB 값을 찾고 MSB값이 바뀌는 순간의 K를 반환
*/
int find(int val){
    for(int i = 1; ;i = i<<1){
        if(val/i == 1){
            return i - 1;
        }
    }
}

int main(){
    int T, val;
    scanf("%d", &T);
    for(int t =1; t<T+1; t++){
        scanf("%d", &val);
        printf("%d\n", find(val));
    }
}
```



## 주의 사항

첫 번째 문제로 설명도 간단하고 코드 자체도 간단하다. 실제 contest에서는 이런 문제는 빠르게 파악하고 간단하게 코드를 구성하는게 중요할 거 같다.