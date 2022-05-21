---
use_math: true
title: "Exciting Bets (#A)"
subtitle: "Codeforces Round 730 Div.2"
categories: algorithm
tags: codeforces
---

## 문제파악

a와 b 두 수가 주어졌을 때 두 수를 동시에 1 증가 시키거나(1) 둘 다 0 이상인 경우 1을 감소시키는(2) 두 가지 연산을 사용하여 만들어낸 두 수의 GCD(최대 공약수) 중 가장 큰 값과 해당 GCD를 만들어 내기 위한 최소 연산 횟수를 계산해라

## IDEA

1. 구해야 하는 GCD의 특성을 고려해야 한다

2. (1) (2) 두 연산을 몇 번을 수행하여도 두 수의 차이(|a-b|)는 변하지 않는다

3. a > b, GCD(a, b) = GCD(a-b, b)

   > $GCD(a,b) = k$
   >
   > $a = a_1*k, b = b_1*k$
   >
   > $a-b = k*(a_1 - b_1)$​
   >
   > $따라서, GCD(a-b, b) = GCD(k*(a_1 - b_1), k*b_1) = k = GCD(a,b)$

4. 두 수의 차이는 고정이기 때문에 가능한 최대 GCD의 값은 두 수의 차가 되며 이 값을 만들기 위해 b가 얼마나 증가하거나 감소되어야 하는지 계산하면 된다




## CODE

```c++
#include <stdio.h>
 
typedef unsigned long long ull;
 
#define MIN(x,y)    ((x) < (y) ? (x) : (y))
 
int main(){
    int T;
    ull a, b, sm, bi, count, result;
    scanf("%d", &T);
 
    for(int t= 0; t<T; t++){
        count = result = 0;
        bi = sm = 0;
        scanf("%llu %llu", &a, &b);
        if(a == b){
            printf("0 0\n");
            continue;
        }
        if(a < b){
            sm = a;
            bi = b;
        }
        else{
            sm = b;
            bi = a;
        }
 
        result = bi - sm;
        // b가 증가하는 것과 감소하는 것 둘 중 횟수가 적은 것을 선택
        count = MIN(result - sm%result, sm % result); 
        printf("%llu %llu\n", result, count);
    }
}
```



## 주의 사항

두 수의 차이가 변화가 없다는 것은 쉽게 알아 챌 수 있었지만 GCD의 특성인$GCD(a,b) = GCD(a-b,b)$​ 이 수식을 찾지 못하였다. 차이가 변화가 없다는 성질을 가지고 이용을 어떻게 할 수 있을지 고민을 했어야 했다

그리고 제한사항에 따라서 a,b 는 *int* 로 저장할 수 없음을 유의 해야 한다.