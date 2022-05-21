---
use_math: true
title: "Increase Subarray Sums (#C)"
subtitle: "Codeforces Educational Round 123 Div.2"
categories: algorithm
tags: codeforces
---

## 문제파악

정수로 이뤄진 수열에서 임의의 k 위치에 x 만큼 값을 추가했을 때 부분수열의 합 중 최대가 되는 값을 k 가 0 부터 n 까지 모두 구해라

## IDEA

1. 추가되는 x 는 양의 정수이다
    > 가능한 최대한 추가하는 것(k 만큼)이 최대
2. 길이가 k 보다 작은 경우에는 길이만큼만 x 를 추가할 수 있다.
    > 최대로 추가할 수 있는 값은 MIN(k, n) * x 가 된다.
3. 2를 통해 추가되는 값은 오로지 길이에만 영향을 받게 된다는 것을 알 수 있다.
    > 길이별 최대가 되는 부분수열은 항상 고정이며 k에 따라 값만 변경된다.
4. 길이 n 의 최대값은 5000
    1. O(n^2 = 25000000) 안으로만 해결하면 된다.
    2. 따라서 길이에 따른 모든 부분 수열을 구할 수 있다
5. 길에마다 최대 부분수열은 k와 상관없이 고정으로 f(k)을 계산할 때 오로지 길이 당 한 개의 부분수열의 값만을 확인하면 f(k) 계산 가능

## CODE

```c++
#include <stdio.h>
#include <vector>

using namespace std;

#define MAX(x, y)   ((x) > (y) ? (x) : (y))
#define MIN(x, y)   ((x) < (y) ? (x) : (y))

int main(){
    int T, N, X;
    scanf("%d", &T);

    for(int t =0; t<T; t++){
        scanf("%d %d", &N, &X);
        vector<int> arr(N);
        vector<int> count(N+1);

        for(int n = 0; n<N; n++){
            scanf("%d", &arr[n]);
        }

        // fill count
        for(int l = 1; l<=N; l++){
            int sum = 0;

            for(int i = 0; i<l; i++){
                sum += arr[i];
            }

            count[l] = sum;

            for(int n = l; n < N; n++){
                sum += arr[n];
                sum -= arr[n-l];

                count[l] = MAX(count[l], sum);
            }
        }

        // get result
        for(int k = 0; k<=N; k++){
            int max = 0;

            for(int l = 1; l<=N; l++){
                max = MAX(max, count[l] + MIN(k, l) * X);
            }

            printf("%d ", max);
        }
        printf("\n");
    }
}
```

## 주의사항

1. 문제에 적용할 수 있는 Time Complexity 수준을 잘 파악해야 한다.
    > 처음 문제를 접했을 때 max subarray 라는 것에 갇혀 sliding window 방식의 알고리즘을 생각하려고 했고 구현 및 아이디어에서 문제가 있어 결국 해당 방법으로 풀 수 없었다. 위 내용을 보면 알 수 있듯이 O($n^2$) 수준의 방식으로 간단한 풀이를 사용할 수 있었다.
2. 문제 분석을 철저히 해서 문제에서 고려해야 하는 부분이 어떤 것인지 파악해야 한다.
    > 해당 문제는 길이와 k 만이 영향을 끼치는 것을 파악하는 것이 핵심이라고 보인다. 처음 문제를 접했을 당시 길이를 크게 고려할 대상으로 보지 않아 좋지 않은 방식으로 풀이하려 했다. 분석을 통해 직접적인 영향이 있는 요소들을 발견하는 게 중요했다고 생각한다.
    