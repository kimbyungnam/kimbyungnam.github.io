---
layout: post
title: "Two Tables (#B)"
subtitle: "Codeforces Educational Round 112 Div.2"
categories: algorithm
tags: codeforces
---

## 문제파악

W(가로), H(세로) 길이를 가진 공간 위에 좌하단 우상단 좌표를 가진 직사각형(R1)과 가로와 세로 길이가 주어진 직사각형(R2)을 배치할 수 있게 하기 위해서 R1을 움직이는 최소 길이를 구해라 또는 그럴 수 없다면 -1을 반환 

## IDEA

1. 직사각형 간 서로 접하지 않는다면 두 도형 간에 수직 또는 수평적인 선을 그을 수 있다는 것이다.

   > 즉 겹치는 부분을 없애기 위해서는 수직 또는 수평적인 움직이는 것이 최소가 된다 -> 대각선 움직임은 낭비

2. 따라서 두 직사각형을 떨어지게 두기 위해서는 두 직사각형의 가로 길이의 합이 W 이하이거나 세로 길이의 합이 H 이하여야 한다

3. 수직 또는 수평 이동의 경우의 수는 4가지이다( 상,하,좌,우 )


## CODE

```c++
#include <stdio.h>

#define MIN(x, y)   ((x) < (y) ? (x) : (y))
#define MAX(x, y)   ((x) > (y) ? (x) : (y))

int main(){
    int T;
    scanf("%d", &T);

    for(int t=0; t<T; t++){
        int W,H, f_pos[2][2], s_pos[2], result = __INT32_MAX__;
        scanf("%d %d", &W, &H);
        scanf("%d %d %d %d", &f_pos[0][0], &f_pos[0][1], &f_pos[1][0], &f_pos[1][1]);
        scanf("%d %d", &s_pos[0], &s_pos[1]);

        if(s_pos[0] + f_pos[1][0] - f_pos[0][0] <= W){
            result = MIN(result, MAX(0,s_pos[0] - f_pos[0][0]));
            result = MIN(result, MAX(0, f_pos[1][0] - (W - s_pos[0])));
        }
        if(s_pos[1] + f_pos[1][1] - f_pos[0][1] <= H){
            result = MIN(result, MAX(0, s_pos[1] - f_pos[0][1]));
            result = MIN(result, MAX(0, f_pos[1][1] - (H - s_pos[1])));
        }

        printf("%d\n", result == __INT32_MAX__ ? -1 : result);
    }
}
```



## 주의 사항

1. 직사각형의 움직임이 상하좌우 라는 것을 알아야 한다
2. 좌표를 생각하지말고 직사각형이 움직임만 신경 쓰면 된다
3. 세세한 조건을 고려해야 한다. (ex. -1 반환 등)

실제 문제 풀이시에 1을 파악 했지만 2번 사항에서 R2 사각형의 좌표를 생각하며 코드를 구성해 코드가 복잡해지고 이로 인해 조건문을 하나 빼먹는 실수를 했다. 문제를 최대한 단순화 하는 게 중요하다
