---
use_math: true
title: "Dynamic programming"
subtitle: "Guide to Competitive Programming"
categories: algorithm
tags: basics
---

# 동적 계획법  
문제의 최적해를 구하거나 답의 개수를 세는 과정에 사용할 수 있는 알고리즘 설계 기법  
> 무차별 알고리즘을 효율적으로 구현  

- 점화식을 세울 수 있어야 한다.
- sub solution 을 통해 solution 을 구할 수 있어야 한다.
- 효율적인 연산을 위해 메모이제이션 기법 활용 한다.

### 메모이제이션(Memoization)  
함수의 값을 계산한 뒤 이를 배열에 저장하여 그 값이 다시 필요할 때마다 함수를 새로 호출하지 않고 값을 가져오는 방법  

# 동적 계획법을 활용한 문제 해결  

## 여러 동전 중 특정 가격을 최소한의 동전으로 만들기  
> https://www.acmicpc.net/problem/2294  
```C++
#include <stdio.h>
#include <vector>
#include <cmath>

using namespace std;

#define MAXK    (10000)
#define INF (MAXK + 1)

int solve(vector<int> & coins, vector<int> & dp, int K){
    for(int k=1; k<=K; k++){
        dp[k] = INF;
        for(int i = 0; i<coins.size(); i++){
            if(k >= coins[i])
                dp[k] = min(dp[k], dp[k-coins[i]] + 1);
        }
    }

    return dp[K];
}
/* recursion
int solve(vector<int> & coins, vector<int> & dp, int K){
    if(K < 0)   return INF;
    if(K == 0)  return 0;
    if(!(dp[K] < 0))    return dp[K];

    int best = INF;
    for(int i = 0; i<coins.size(); i++){
        best = min(best, solve(coins, dp, K - coins[i]) + 1);
    }
    dp[K] = best;
    return best;
}
*/

int main(){
    int N, K;
    scanf("%d %d", &N, &K);
    vector<int> coins(N);
    vector<int> dp(K+1);
    for(int n = 0; n<N; n++){
        scanf("%d", &coins[n]);
    }
    printf("%d\n", solve(coins, dp, K) == INF ? -1 : dp[K]);
}
```

## 최장 증가 부분 수열의 길이 구하기  
> https://www.acmicpc.net/problem/11053  
```C++
#include <stdio.h>
#include <vector>
#include <cmath>
#include <algorithm>

using namespace std;

/*
O(NlogN)
*/
int solve(vector<int> & input){
    int N = input.size();
    vector<int> dp(N+1);

    dp[1] = input[0];
    int S = 1, E = 1;

    for(int i = 0; i<input.size(); i++){
        int s = S, e = E;
        while (s < e){
            int m = (s+e+1)/2;

            if(dp[m] < input[i]){
                s = m;
            }
            else{
                e = m - 1;
            }
        }
        if(dp[s] < input[i]){
            dp[s+1] = input[i];
            E = max(E, s+1);
        }
        else{
            dp[s] = input[i];
        }
    }
    return E;
}

/*
O(N^2)
*/
int solve(vector<int> & input){
    vector<int> dp(N, 1);

    for(int i = 0; i<input.size(); i++){
        for(int j = 0; j<i; j++){
            if(input[j] < input[i]){
                dp[i] = max(dp[i], dp[j] + 1);
            }
        }
    }
    return *max_element(dp.begin(), dp.end());
}

int main(){
    int N;
    scanf("%d", &N);
    vector<int> input(N);
    for(int n = 0; n<N; n++){
        scanf("%d", &input[n]);
    }
    printf("%d\n", solve(input));
}
```


## 격자상의 경로 중 최적 경로 구하기  
메모이제이션에서 2차원 배열을 활용하는 것이 가능하다  
> https://www.acmicpc.net/problem/10164  
```C++
#include <stdio.h>
#include <vector>

using namespace std;

int main(){
    int N, M, K, result;
    scanf("%d %d %d", &N, &M, &K);
    pair<int, int> S = {0, 0}, E = {N - 1, M - 1};
    vector<vector<int>> dp(N, vector<int>(M, 0));

    for(int i = 0; i<M; i++){
        dp[0][i] = 1;
    }
    for(int i = 0; i<N; i++){
        dp[i][0] = 1;
    }

    for(int i = 1; i<N; i++){
        for(int j = 1; j<M; j++){
            dp[i][j] = dp[i-1][j] + dp[i][j-1];
        }
    }

    if(K){
        pair<int, int> mid = {(K-1)/M, (K-1)%M};
        result = dp[mid.first][mid.second] * dp[E.first - mid.first][E.second - mid.second];
    }
    else{
        result = dp[E.first][E.second];
    }

    printf("%d\n", result);
}
```

## 짐 싸기(Knapsack) 문제  
> https://www.acmicpc.net/problem/12865  
```C++
#include <stdio.h>
#include <vector>
#include <cmath>

#define MAXV    (1000)

using namespace std;

int main(){
    int N, K;
    scanf("%d %d", &N, &K);
    vector<pair<int, int>> stuff(N);// W, V
    for(int n = 0; n<N; n++){
        scanf("%d %d", &stuff[n].first, &stuff[n].second);
    }
    /*
    dp[i][j] -> i : 물건 번호, j : 무게 = 최대 V
    최종 -> dp[N][K] = dp[N-1][K - S[N].W] + S[N].V or dp[N-1][K]
    */

    vector<vector<int>> dp(N + 1, vector<int>(K+1, 0));
    for(int i = 1; i<=N; i++){
        for(int k = 1; k<=K; k++){
            pair<int, int> S = stuff[i-1];
            if(k >= S.first){
                dp[i][k] = max(dp[i][k], dp[i-1][k - S.first] + S.second);
            }
            dp[i][k] = max(dp[i][k], dp[i-1][k]);
        }
    }

    printf("%d\n", dp[N][K]);
}
```

## 순열 대신 부분집합을 활용  
순열의 시간 복잡도(n!) 보다는 부분집합의 시간 복잡도($2^n$)을 활용하는 것이 좋다  

## 배치 가능한 타일 경우의 수  
> https://www.acmicpc.net/problem/2718  
```C++
#include <stdio.h>
#include <vector>

using namespace std;

bool check(unsigned int lower, unsigned int upper, int N){
    if(lower & upper){
        return false;
    }
    unsigned int combine = ~(lower | upper) & ((1U<<N) - 1);
    while(combine){
        switch (combine & 0b11U)
        {
        case 0b11:
            combine = combine >> 2;
            break;
        case 0b00:
            combine = combine >> 2;
            break;
        case 0b01:
            return false;
        case 0b10:
            combine = combine >> 1;
            break;
        }
    }
    return true;
}

int main(){
    int T, N;
    scanf("%d", &T);
    for(int t = 1; t<=T; t++){
        scanf("%d", &N);

        //dp[i][j] -> i : 세로, j : 해당 층 패턴
        //dp[N][K] = + dp[N-1][K & K`== 0]
        
        vector<vector<int>> dp(N, vector<int>(1<<4, 0));
        for(int i = 0; i<1<<4; i++){
            dp[0][i] = check(i, 0, 4);
        }

        for(int i = 1; i<N; i++){
            for(unsigned long long j = 0; j<1<<4; j++){
                for(unsigned long long k = 0; k<1<<4; k++){
                    if(check(j, k, 4)){
                        dp[i][k] += dp[i - 1][j];
                    }
                }
            }
        }
        // printf("result: ");
        printf("%d\n", dp[N-1][0]);
    }
}
```
