---
layout: post
title: "Sequence Pair Weight (#C)"
subtitle: "Codeforces Round 721 Div.2"
categories: algorithm
tags: codeforces
---

## 문제파악

숫자 배열에서 같은 숫자끼리 2개 씩 그룹을 지을 수 있는 경우의 수를 해당 배열의 값이라고 할 때, 배열의 모든 부분 배열의 값의 합을 구해라( 부분 배열은 배열의 시작 과 끝에서 요소를 0개 이상 제거해서 만들어진 배열)



## IDEA

1. 단순히 모든 부분 배열을 만들고 값을 계산하기에는 부분배열의 경우의 수가 너무 많다

2. 같은 숫자만을 그룹 지을 수 있기 때문에 갯수에 각 배열의 요소 값마다 독립적으로 계산이 가능하다

3. 만약 배열 $a_i$의 값을 알고 있을 때 끝에 새로운 값이 추가하면서 변경되는 값을 구하며 기존 a 배열의 값을 계산하는 점진적인 방법이 가능하다

   > 단순 탐색은 계산하는 수가 너무 많다

4. **기존에 없던 요소가 추가 되면 값에 영향이 없지만**, 추가된 요소가 $b_i$(i 위치 요소)와 같고 유일 했다면 **값에는 `$b_i$`가 추가**된다 


## CODE

```c++
#include <bits/stdc++.h>

using namespace std;

#define MAXN (100000)

typedef unsigned long long ull;

int input[MAXN];

int main(){
    int T, N;
    scanf("%d", &T);

    for(int t= 1; t<T+1; t++){
        scanf("%d", &N);

        map<int, ull> count;
        ull result = 0, round = 0;

        for(int n = 0; n <N; n++){
            scanf("%d", &input[n]);
        }        

        for(int i = 0; i<N; i++){
            if(count.count(input[i])){
                round += count[input[i]];
                count[input[i]] += i+1;
            }
            else{
                count[input[i]] = i+1;
            }

            result += round;
        }

        printf("%llu\n",  result);
    }
}
```



## 주의 사항

1. 단순 탐색으로는 시간이 오래 걸린다는 것을 파악 해야 한다
2. 배열을 확장해 나가는 점진적인 방식을 떠올려야 한다
3. 작은 테스트 케이스에 직접 시뮬레이션을 하면서 아이디어를 떠올릴 수 있었다

