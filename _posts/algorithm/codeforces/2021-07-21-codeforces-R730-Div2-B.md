---
use_math: true
title: "Customising the Track (#B)"
subtitle: "Codeforces Round 730 Div.2"
categories: algorithm
tags: codeforces
---

## 문제파악

레이싱 경기를 하기 위한 sub-track들이 존재하는데 트래픽의 방해를 최소한으로 받게 트래픽 차량들을 옮기고 트랙의 최소 방해도를 계산해야 한다

> 방해도 = $\displaystyle\sum_{i=1}^n\displaystyle\sum_{j=i+1}^n{|a_i - a_j|}$​



## IDEA

1. 방해도가 최소가 되려면 각 sub-track들의 차이가 최소가 되어야 한다
2. 즉, 차량을 최대한 평등하게 분배를 해야 한다
3. 차량 이동 횟수나 방법에 제한이 없기 때문에 각 sub-track 간 차이는 1을 넘기지 않는다
4. 따라서 각 sub-track의 트래픽은  p 또는 p+1이며 p인 sub-track의 개수는 $sum\ mod\ n$​ 이며 p+1인 sub-track의 개수는 $n - sum\ mode\ n$이다




## CODE

```c++
#include <stdio.h>
 
int main(){
    int T, N, input;
    unsigned long long int result;
    scanf("%d", &T);
 
    for(int t = 1; t<T+1; t++){
        result = 0;
        scanf("%d", &N);
        for(int n = 0; n<N; n++){
            scanf("%d", &input);
            result += input;
        }
        result = (result%N) * (N-result%N);
        printf("%llu\n", result);
    }
}
```



## 주의 사항

방해도의 수식을 파악하고 차량의 분배가 최대한 평등해야 한다는 것을 도출해내는 것이 가장 중요하다

p 그리고 p+1인 트랙의 갯수를 파악하는 수식을 최대한 단순하고 정확하게 구성해야 한다

모든 sub-track의 차량 총합을 연산 과정에서 사용하게 되는데 문제의 제한 사항에 따라 int 타입으로 처리할 수 없는 점을 유의해야 한다