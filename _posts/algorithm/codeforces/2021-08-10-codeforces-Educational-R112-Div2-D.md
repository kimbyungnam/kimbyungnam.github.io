---
layout: post
title: "Say No to Palindromes (#D)"
subtitle: "Codeforces Educational Round 112 Div.2"
categories: algorithm
tags: codeforces
---

## 문제파악

a, b, c 총 3개의 문자만으로 이뤄진 문자열에서 모든 부분 문자열이 palindrome을 이루지 않는 것을 beutiful 하다라고 한다면 주어진 문자열을 beautiful 하게 만들기 위해서 변경해야 하는 최소 문자 갯수를 구해라



## IDEA

1. 문자열이 오로지 `3 개의 문자`로만 이뤄진다
2. 길이가 3인 부분 문자열이 beutiful 이려면 각 문자가 서로 달라야 한다
3. 길이가 4인 부분 문자열을 생각해보면
   1. 0 번째 부터 시작하는 문자열과 1부터 시작하는 문자열 그리고 길이가 4인 문자열 이 3 개의 문자열이 모두 beutifule 이여야 한다.
   2. 이는 **0번째 문자와 3번째 문자는 같아야 한다**
4. 3에 따라서 전체 문자열이 beatiful을 유지하려면 **3개의 문자가 동일한 순서로 반복적으로 나열되여야만 한다**




## CODE

```c++
#include <stdio.h>
#include <vector>
#include <algorithm>

using namespace std;

#define MIN(x, y)   ((x) < (y) ? (x) : (y))

#define MAXN (2*100000 + 1)

int main(){
    int N, M, l, r, cost;
    char input[MAXN];

    scanf("%d %d", &N, &M);
    scanf("%s", input);

    vector<char> part = {'a', 'b', 'c'};
    vector<vector<int>> count_pr(6, vector<int>(N+1, 0));

    int j = 0;
    do{
        for(int l_c = 1; l_c<=N; l_c++){
            count_pr[j][l_c] += (part[(l_c-1)%3] != input[l_c-1]) + count_pr[j][l_c-1];
        }
        j++;
    } while(next_permutation(part.begin(), part.end()));


    for(int m = 0; m < M; m++){
        scanf("%d %d", &l, &r);
        cost = __INT32_MAX__;

        // auto 금지
        for(int i = 0; i<6; i++){
            cost = MIN(cost, count_pr[i][r] - count_pr[i][l-1]);
        }
        
        printf("%d\n", cost);
    }
}
```



## 주의 사항

1. 문제의 제한사항인 3개의 문자로만 이뤄진다는 점을 파악해야 한다
2. 주어진 테스트 케이스를 직접 수행해보는 것이 아이디어를 파악하기 쉽게 한다
3. `auto` 를 쓰는 것에 주의해야 한다. 위 코드에서 마지막 최솟값을 찾을 때 `auto` 를 사용하게 되면 시간초과가 된다

