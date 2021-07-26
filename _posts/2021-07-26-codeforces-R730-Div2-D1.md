---
layout: post
title: "Need for Pink Slips (#C)"
subtitle: "Codeforces Round 730 Div.2"
categories: algorithm
tags: codeforces
---

## 문제파악

0 부터 n 사이의 비밀번호를 추측해야 한다. 총 n 번의 추측 기회가 주어지며 추측한 내용이 비밀번호와 다를 경우에는 비밀번호가 아래의 식을 이용하여 갱신된다

> $x \bigoplus_k z = y$​, (x는 기존 비밀번호, z는 갱신된 비밀번호, y 추측한 비밀번호​)

## IDEA

1. 비밀번호 추측의 결과를 단순히 틀렸다 맞았다만 알 수 있기에 전탐을 해야 한다

2. 비밀번호는 0부터  n 사이이며 기회는 n 번의 기회가 주어져 0 ~ n-1 까지 모든 경우의 수를 테스트 할 수 있다

3. 전체 탐색을 단 한번 씩만 하기 위해서는 이전의 추측의 영향을 지우고 새로운 추측의 영향을 동시에 결과에 반영해야 한다.

4. 문제의 제한 조건으로 k는 2 이므로 $\bigoplus_2$은 역연산과 동일하다.

   > 같은 수를 두 번 연산하면 아예 연산하지 않은 것과 같다는 것이다

5. 따라서, 0부터 n까지 전체를 시도한다고 할 때 i를 시도할 때는 $(i-1) \bigoplus_2 i$를 추측하면 된다


## CODE

```c++
#include <stdio.h>

int main(){
    setbuf(stdout,NULL);
 
    int T, N, K, result, prev;
    scanf("%d", &T);
    for(int t = 0; t< T; t++){
        scanf("%d%d", &N, &K);
 
        printf("0\n");
        scanf("%d", &result);
        if(result){
            continue;
        }
        for(int n = 1; n<=N; n++){
            printf("%d\n", (n-1) ^ n);
            scanf("%d", &result);
            if(result == 1)
                break;
        }
    }
}
```



## 주의 사항

k가 2로 고정되어 비트 연산만을 생각하면 D2에 비하여 비교적 쉽게 접근할 수 있다

XOR 연산의 중복은 제거된다는 점이 중요하다

n번의 기회로 딱 한 번씩 전체 탐색을 할 수 있다는 점이 힌트가 될 수 있다