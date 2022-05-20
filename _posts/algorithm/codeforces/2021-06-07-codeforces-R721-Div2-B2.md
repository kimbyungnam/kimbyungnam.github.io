---

title: "Palindrome Game (hard version) (#B2)"
subtitle: "Codeforces Round 721 Div.2"
categories: algorithm
tags: codeforces
---

## 문제파악

Palindrome Game (easy version) (#B1)과의 차이점은 첫 번째 문자열이 Palindrome이 아닐 수 있다는 점이다


## IDEA

1. 첫 번째 문자열이 Palindrome인 경우 #B1 인 경우와 똑같이 처리하면 된다
2. **Alice는 Palindrome을 되기 전까지 Op.2를 하므로써 Bob에게 Op.1을 강제**할 수 있다
3. **1개의 0을 바꿈으로써 Palindrome이 될 수 있는 순간**에 Alice는 #B1의 전략을 사용하면 항상 Bob보다 비용을 적게 쓰게 된다.
4. 하지만 예외로 길이가 홀수이며 **가운데를 포함하여 0이 두 개인 경우**에는 Alice가 Op.2를 수행한 후에 Bob도 Palindrome 문자열을 만들어내 Alice에게 Op.1을 강제할 수 있기에 무승부가 된다.


## CODE

```c++
#include <stdio.h>

#define MAXN (1000)

int main(){
    int T, N, count, palin_count;
    char input[MAXN+1];
    scanf("%d", &T);
    for(int t=1; t<T+1; t++){
        count = 0;
        palin_count = 0;

        scanf("%d", &N);
        scanf("%s", input);

        for(int i = 0; i < N; i++){
            count += input[i] == '0';
            palin_count += (input[i] != input[N-1-i]);
        }

        // same as #B1 problem
        if(!palin_count){
            // palindrome case
            if(count == 1 || ((count & 1) == 0)){
                printf("BOB\n", count);
            }
            else{
                printf("ALICE\n", count);
            }
        }
        else{
            if((count == 2) && (palin_count == 2))
                printf("DRAW\n");
            else
                printf("ALICE\n");
        }
    }
}
```



## 주의 사항

#B1 문제를 잘 접근했다면 이 문제의 전략을 파악하는 것은 비교적 쉬웠을 수 있다. 다만 #B1 문제와 유사하게 예외 케이스의 존재를 파악하는 것이 중요하다.