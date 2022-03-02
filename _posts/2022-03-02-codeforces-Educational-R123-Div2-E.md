---
layout: post
title: "Expand the Path (#E)"
subtitle: "Codeforces Educational Round 123 Div.2"
categories: algorithm
tags: codeforces
---

## 문제파악

n*n 지도에서 1,1 에서 시작하여 하단(D), 우측(R) 으로 1칸 이동하는 명령이 문자열로 주어질 때 인접하는 명령을 최대한 반복하여 지도에서 방문할 수 있는 칸의 수를 반환해라 

## IDEA

1. 방문할 수 있는 칸들의 영역을 살펴보면 빈 틈이 없는 도형이 되는 것을 확인할 수 있다.
    > 우상단 및 좌하단이 비어있는 도형
2. 경로의 가짓수 또는 경로에 점 단위 등으로 생각하여 연산 시 중복되는 영역 처리가 힘들다.
    > 전탐은 불가 Space Complexity = O($n^2 \le 10^16$)
3. 1, 2에 따라서 최종적으로 방문할 수 있는 칸의 영역의 넓이를 구하는 방식으로 접근이 가능하다.
    > 경계 지점이 중요
4. 명령 복사를 추가하게 되면 해당 위치 앞 뒤의 명령에 영향을 끼치지 않는다.
5. 우상단의 경계가 되는 부분을 고려해보면
    1. 최대로 우상단이 되기 위해서는 최대한 우측으로 이동한 후 하단이동을 해야 한다.
        > 최대로 우측으로 가기전에 하단이동을 하게 되면 최대로 우측으로 이동했을 경우의 우상단 영역을 포함하지 않게 된다
    2. 따라서 R이 첨으로 등장하는 위치에 최대한(경계를 넘지 않도록) R을 추가하는 경우가 우상단의 경계가 되는 영역을 지나간다.
6. R, D를 서로 바꾸게 되면 도형의 넓이는 같되 좌하단의 영역이 우상단으로 가게 된다.
    > 5 번의 내용을 그대로 좌하단 영역에 적용이 가능하여 구현을 쉽게 할 수 있다.
7. 영역의 넓이는 (전체 넓이 - 포함하지 않는 영역(좌하단, 우상단)) 으로 구할 수 있다.
    > 계산 편의성

## CODE

```c++
#include <stdio.h>
#include <string>

#define MAXN    (100000000)

using namespace std;

long long sub_process(int N, string & input){
    long long sum = 0;
    int len = input.size(), count = 0;
    int i;

    for(i = 0; i<len && input[i] != 'R'; i++){
        sum += N - 1;
    }

    for(int j = len - 1; j>= i; j--){
        switch (input[j])
        {
        case 'D':
            sum += count;
            break;
        case 'R':
            count += 1;
            break;

        default:
            // error
            break;
        }
    }

    return sum;
}

int check_corner(int N, string & input){
    int prev = input[0];
    for(int i = 0; i < input.size(); i++){
        if(input[i] != prev)
            return 0;
        prev = input[i];
    }
    return 1;
}

long long process(int N, char * input){
    string o(input);
    string r(input);
    long long sum = 0;

    for(int i = 0; i<r.size(); i++){
        r[i] = (r[i] == 'R' ? 'D' : 'R');
    }

    if(check_corner(N, o)){
        return N;
    }
    
    sum = sub_process(N, o);
    sum += sub_process(N, r);

    return (long long)N*N - sum;
}

char input[MAXN + 1];
int main(){
    int T, N;

    scanf("%d", &T);

    for(int t = 0; t<T; t++){
        scanf("%d", &N);
        scanf("%s", input);

        long long result = process(N, input);

        printf("%lld\n", result);
    }
}
```

## 주의사항

1. 주어진 테스트 케이스 또는 데이터 등을 자세히 분석하는 것이 중요하다.
    > 경로라는 점에 집중하여 점들이 모여 영역으로 생각하여 풀 수 있다는 생각을 하지 못했다.
2. 같은 방식이더라도 구현을 최대한 간단하게 할 수 있는 방법을 떠올려야 한다.
    > 해당 시험 자체가 2시간 제한으로 구현을 간단하게 할수록 유리하고 디버깅에도 시간이 덜 들게 된다.
