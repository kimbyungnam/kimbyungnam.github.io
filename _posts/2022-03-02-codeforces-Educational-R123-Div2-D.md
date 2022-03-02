---
layout: post
title: "Cross Coloring (#D)"
subtitle: "Codeforces Educational Round 123 Div.2"
categories: algorithm
tags: codeforces
---

## 문제파악

n*m 사이즈의 종이에서 한 점의 가로 세로를 모두 특정 색으로 칠하는 연산을 q 번 진행했을 때 결과물이 서로 다른 경우의 수를 반환해라 

## IDEA

1. n, m 의 최대값은 $2*10^5$ 으로 n*m 이 상당히 크다
    > 2d 배열로 접근하기 어렵다
2. 2차원이 아닌 1차원 종이에서 점을 칠하는 연산이라고 축소하여 생각을 해 볼 수 있다.
    > q 개의 점 중에서 중복되는 점의 개수를 제외한 수가 q' 이라 하면 결과물의 개수는 $k^{q'}$ 가 된다.
3. 위 2번을 2차원 확장 시켜도 똑같이 중복되어 최종적으로 영향이 없는 연산을 제외한 수가 q' 이라 하면 결과물의 개수는 $k^{q'}$ 가 된다.
4. 앞에서의 연산 결과는 뒤에 오는 연산에 의해 덮어씌워진다.
    1. 같은 열(또는 행)의 점이 선택되는 경우 이전에 해당 열(또는 행)에 대한 연산은 다 덮어씌워진다.
    2. 모든 열(또는 행)에 대해서 연산이 수행된 경우 이전 연산은 다 덮어씌워진다.
5. 따라서 구현을 위해서 연산을 뒤에서 부터 확인하여 덮어씌워질 연산은 제외하여 수를 센 후 결과물을 계산한다.

## CODE

```c++
#include <stdio.h>
#include <vector>
#include <set>

using namespace std;

#define MOD (998244353)

#define MAX(x, y)   ((x) > (y) ? (x) : (y))

int cal_result(int n, int r)
{
    if(r == 0)
        return 1;

    return (n * cal_result(n, r-1)) % MOD;
}

int main(){
    int T, N, M, K, Q, X, Y;

    scanf("%d", &T);

    for(int t = 0; t< T; t++){
        scanf("%d %d %d %d", &N, &M, &K, &Q);
        
        // vector<int> row(N+1, 0), col(M+1, 0);
        set<int> row, col;
        vector<pair<int, int>> queries(Q+1);
        int count = 0, rc = 0, cc = 0, result = 1;

        for(int q = 1; q<Q+1; q++){
            scanf("%d %d", &X, &Y);
            queries[q] = {X, Y};
        }

        for(int q = Q; q>0; q--){
            X = queries[q].first; Y = queries[q].second;

            if(row.count(X) && col.count(Y)){
                continue;
            }

            row.insert(X);
            col.insert(Y);

            result = result * 1ll * K % MOD;

            if(row.size() == N || col.size() == M){
                break;
            }
        }

        printf("%d\n", result);
    }
}
```

## 주의사항

1. corner case 에 대한 고려를 항상 해야 한다.
    > 처음 문제 접했을 때 모든 행 또는 열이 칠해지는 경우를 고려하지 못하여 조건문 하나를 빼먹어 풀지 못했었다.
2. stl 또는 사용되는 자료구조에 대한 이해가 필요하다.
    > row, col 에 확인을 vector 로 하게 되면 TLE가 발생한다.
3. 순열, 조합, 중복순열, 중복조합 등 기본적이 수학은 알아야 한다.
    > 사전에 공부하지 않으면 문제 풀 때 상당히 헷갈리고 계산하는 방법을 떠올리지 못할 수 있다.
    