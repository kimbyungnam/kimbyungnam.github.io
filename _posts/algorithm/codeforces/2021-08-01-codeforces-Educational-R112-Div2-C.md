---

title: "Coin Rows (#C)"
subtitle: "Codeforces Educational Round 112 Div.2"
categories: algorithm
tags: codeforces
---

## 문제파악

2 개의 행과 m 개의 열을 가진 지도에서 1, 1 위치에서 Alice가 먼저 출발하여 2, m 위치 까지 하 또는 우측 으로만 움직이며 지도에서 지나간 곳에 코인을 먹는 경우 Bob이 최대한 코인을 먹으려고 할 때 얼마나 먹을 수 있는지 구해라

## IDEA

1. 할 수 있는 연산이 오른쪽 이동 또는 아래로 이동 두 가지로 제한되어 다시 돌아올 수 없다.

   > 이 제한으로 인해 Alice가 가느 경로의 모양은 아래와 같이 정형화 된다.
   >
   > ![image](https://user-images.githubusercontent.com/32065940/127761154-e2d781e8-ac2c-4170-b319-82fe3a8ac6b8.png)

2. Bob은 최대한 많은 코인을 먹으려고 하기 때문에 위 그림에서 좌 하단 또는 우 상단의 Alice가 먹지 않은 곳을 먹게 된다.


## CODE

```c++
#include <stdio.h>

#define MIN(x, y)   ((x) < (y) ? (x) : (y))
#define MAX(x, y)   ((x) > (y) ? (x) : (y))

typedef unsigned long long ull;

int main(){
    int T;
    scanf("%d", &T);

    int map[2][100000];
    int sum[2][100000] = {0,};
    for(int t=0; t<T; t++){
        int M;
        scanf("%d", &M);

        for(int i = 0; i<2; i++){
            sum[i][0] = 0;
            for(int j = 0; j<M; j++){
                scanf("%d", &map[i][j]);
                sum[i][j] = map[i][j];
                if(j > 0)
                    sum[i][j] += sum[i][j-1];
            }
        }

        ull result = -1;

        for(int j = 0; j<M; j++){
            result = MIN(result, MAX(sum[1][j] - map[1][j], sum[0][M-1] - sum[0][j]));
        }

        printf("%llu\n", result);
    }
}
```



## 주의 사항

1. 실제로 지도를 그려보는 것으로 Alice의 길이 정형화 된다는 것을 쉽게 파악할 수 있었다
