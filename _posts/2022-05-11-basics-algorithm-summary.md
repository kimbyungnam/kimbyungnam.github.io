---
layout: post
title: "Algorithm Summary"
subtitle: "Guide to Competitive Programming"
categories: algorithm
tags: basics
---

## 문제파악

문(R,G,B)과 키(r,g,b)로만 이뤄진 문자열에서 키보다 문이 먼저 오는 경우이면 NO, 키가 항상 매칭되는 문보다 앞에 있다면 YES를 반환한다.



## IDEA

1. testcase 의 개수(t) 의 최대값이 720으로 매우 작다
2. 문자열의 길이는 항상 6이다 
    > 문과 키가 무조건 하나씩은 존재한다
3. 단순하게 대문자(문)가 소문자(키)보다 앞에 있는지 확인하여 판별 가능



## CODE

```c++
#include <stdio.h>

int main(){
    int T, keys[26] = {0, }, result = 0;
    char input[721];
    scanf("%d", &T);

    for(int t = 0; t<T; t++){
        result = 0;

        for(int i = 0; i<26; i++)
            keys[i] = 0;

        scanf("%s", input);

        for(int i = 0; input[i]; i++){
            if(input[i] < 'a'){
                // upper -> door
                if(!keys[input[i] - 'A']){
                    result = 1;
                    break;
                }
            }
            else{
                // lower -> key
                keys[input[i] - 'a'] = 1;
            }
        }

        printf("%s\n", result ? "NO" : "YES");
    }
}
```
