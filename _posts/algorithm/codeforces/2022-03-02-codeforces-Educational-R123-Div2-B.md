---

title: "Anti-Fibonacci Permutation (#B)"
subtitle: "Codeforces Educational Round 123 Div.2"
categories: algorithm
tags: codeforces
---

## 문제파악

anti-fibonacci 란 <img src="https://latex.codecogs.com/svg.image?p_{i-2}&space;&plus;&space;p_{i-1}&space;\neq&space;p_i&space;(3&space;\le&space;i&space;\le&space;n)" title="p_{i-2} + p_{i-1} \neq p_i (3 \le i \le n)" /> 이 조건을 만족하는 수열을 말한다. n이 주어질 때 길이가 n인 가능한 모든 anti-fibonacci 수열을 반환한다. 


## IDEA

1. 모든 수열을 출력해야 한다.
2. n의 최대값은 50으로 매우 작다
3. 결국 모든 경우의 수를 확인하면 된다. 전탐


## CODE

```c++
#include <stdio.h>
#include <vector>

using namespace std;

void dfs(int N, int level, vector<int> & arr, vector<int> & check, int * count){
    if(*count == 0){
        return;
    }
    
    if(level == N){
        *count -= 1;
        for(int i = 0; i<arr.size(); i++){
            printf("%d ", arr[i]);
        }
        printf("\n");

        return;
    }

    for(int i = 0; i< N; i++){
        if(check[i])
            continue;

        arr[level] = i + 1;

        if(level > 1 && arr[level-2] + arr[level-1] == arr[level]){
            continue;
        }

        check[i] = 1;
        dfs(N, level+1, arr, check, count);
        check[i] = 0;
    }
}

int main(){
    int T, N, count;
    scanf("%d", &T);

    for(int t= 0; t<T; t++){
        scanf("%d", &N);

        vector<int> arr(N, 0);
        vector<int> check(N, 0);
        count = N;

        dfs(N, 0, arr, check, &count);
    }
}
```
