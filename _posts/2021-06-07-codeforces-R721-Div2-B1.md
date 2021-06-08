---
layout: post
title: "Palindrome Game (easy version) (#B1)"
subtitle: "Codeforces Round 721 Div.2"
categories: algorithm
tags: codeforces
---

## 문제파악

0과 1로만 이뤄진 Palindrome 문자열이 주어지고 두 명의 참가자(Alice, Bob)가 턴을 돌아가면서 게임을 진행합니다.
게임은 2가지 연산을 수행할 수 있다. 
> Op.1 1달러를 내고 특정 위치의 0 값을 1로 변경하는 것
> Op.2 현재 문자열이 palindrome이 아니고 직전의 연산이 Op.1인 경우 문자열을 반전시키는 것
첫 번째 연산은 언제나 Alice가 수행한다


## IDEA

1. 비용의 차이의 크기는 승리에 영향이 없다
2. **길이가 짝수인 경우와 홀수인 경우를 분리해서 생각해야 한다.**
   1. **짝수**인 경우 Alice는 첫 번째 턴에서 Op.1을 수행하므로서 **Palindrome이 아닌 문자열만**을 만들어 낸다
   2. **홀수이면서 가운데 위치의 문자가 0인 경우** Alice는 첫 번째 턴에서 Op.1을 수행하면서 가운데 위치 0을 1로 바꾸면서 **Palindrome인 문자열**을 만들어 낼 수 있다.
3. 연산을 통해 Palindrome 문자열을 만들면 상대방의 다음 연산을 Op.1으로 강제할 수 있다.
   1. 가운데 0을 바꾸는 경우가 아니라면 Palindrome 문자열에 Op.1 연산 결과는 Palindrome이 아닌 문자열을 만든다
4. 마지막 0을 바꿔야 하는 순간에 Op.2을 수행하게 되면 상대방이 Op.1 연산을 두 번 더 수행하게 된다
   1. 0이 하나 남기 전까진는 Op.1을 수행하여 상대방의 연산을 강제학고 사용되는 비용을 동일하게 유지하되 마지막에 2 달러의 차이를 벌려서 무조건 우승하게 되는 전략
   2. Alice가 처음 가운데 0을 Op.1을 통해 수행한 경우에도 차이는 1달러 차이로 Alice가 여전히 적은 비용을 사용한 것이 된다.
5. 따라서 길이가 홀수이면서 가운데가 0인 경우는 Alice의 승리이고 그외의 경우 Bob의 승리이며 무승부는 존재하지 않는다
   1. 하지만 0의 개수가 1개일 경우는 Alice가 Op.1을 수행하면서 게임이 끝나기 때문에 이 경우는 예외적으로 Bob이 승리한다


## CODE

```c++
#include <stdio.h>

#define MAXN (1000)
#define UNIT (4)

int main(){
    int T, N, count;
    int input[MAXN+1];
    scanf("%d", &T);
    for(int t=1; t<T+1; t++){
        count = 0;
        scanf("%d", &N);
        
        for(int n = 0; n< N; n++){
            scanf("%1d", &input[n]);
        }

        for(int n = 0; n<N; n++){
            count += (input[n] == 0 ? 1 : 0);
        }
        
        // count가 홀수인 경우는 가운데가 1인 경우
        if(count == 1 || ((count & 1) == 0)){
            printf("BOB\n", count);
        }
        else{
            printf("ALICE\n", count);
        }
    }
} 
```



## 주의 사항

이 문제를 처음 접했을 때 가장 중요하게 생각했던 것은 Alice와 Bob이 이기기 위해서 두는 전략을 구상하는 것이다.
이 과정에서 **문자열의 가운데가 0인 경우 Alice 또한 Palindrome 문자열을 만들 수 있다는 사실**을 생각해내는 것이 가장 큰 장애물이다.
실제로 contest에 임했을 당시에 홀수와 짝수 그리고 가운데가 0인 부분을 나눠서 고려하지 않고 모두 짝수인 경우처럼 생각하여 아예 틀린 전략이 나와 틀렸었다.