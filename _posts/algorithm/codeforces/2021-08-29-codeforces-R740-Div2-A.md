---

title: "Simply Strange Sort (#A)"
subtitle: "Codeforces Round 740 Div.2"
categories: algorithm
tags: codeforces
---

## 문제파악

길이가 n이고 1 부터 n 까지 각 다른 수로 이뤄진 수열이 주어집니다. f(i)를 수열의 i 번째 요소가 i+1 번째 요소보다 큰 경우 값을 서로 변경하는 연산이라고 할 때 이를 반복하여 수열을 정렬해야 합니다. 수열을 여러 회차의 반복으로 수행하며 짝수 번째에서는 f(2), f(4), ... f(n-1)을 수행하고 홀수 번째에는 f(1), f(3), ..., f(n-2)를 수행합니다. 몇번의 반복이 필요한지 구해야 합니다.



## IDEA

1. n의 크기 제한이 3 <= n <= 999 이고 모든 테스트 케이스의 n의 총합 또한 999를 넘길 수 없다

   > n의 크기가 상당히 작다

2. 1번의 연산 수행 복잡도는 짝수와 홀수 번째 모두 동일하게 O(N)이다

3. 최악의 경우 N 번의 연산을 수행해야 한다

4. 따라서 단순히 Brute Force를 통해 반복 횟수를 구하여도 O(N^2)의 복잡도를 가지기에 N의 크기 제한이 작으므로 충분하다




## CODE

```c++
#include <stdio.h>
#include <vector>
#include <algorithm>
 
#define MAX(x, y)   ((x) > (y) ? (x) : (y))
#define ABS(x)  ((x) > 0 ? (x) : (-(x)))
 
using namespace std;
 
int main(){
    int T, N, input, max;
    scanf("%d", &T);
 
    for(int  t=0; t<T; t++){
        scanf("%d", &N);
 
        max = 0;
        vector<pair<int,int>> arr(N, {0, 0});
        
        for(int n = 0; n<N; n++){
            scanf("%d", &input);
            arr[n] = {input, n};
        }
 
        int answer = 0;
        int check = 0;
        int flag = 0;
 
        for(int n = 0; n< N - 1; n++){
            if(arr[n].first > arr[n+1].first)
                check = 1;
        }
 
        while(check){
            check = 0;
 
            for(int n = flag; n<N-1; n += 2){
                if(arr[n].first > arr[n+1].first){
                    pair<int, int> temp = arr[n];
                    arr[n] = arr[n+1];
                    arr[n+1] = temp;
                }
            }
 
            for(int n = 0; n< N - 1; n++){
                if(arr[n].first > arr[n+1].first)
                    check = 1;
            }
 
            flag ^= 1;
            answer += 1;
        }
 
        printf("%d\n", answer);   
    }
}
```



## 주의 사항

1. 시간 복잡도 계산이 중요하다

   > 시간 복잡도를 생각하지 않아서 단순히 풀 수 있다는 점을 생각 못하고 계산을 하려다가 시간을 많이 허비했다
