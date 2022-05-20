---

title: "Bathroom Stalls"
subtitle: "Google Codejam 2017 Qualification Round"
categories: algorithm
tags: codejam
---

## 문제파악

단일 행의 공간에 K 명의 사람을 한 명 씩, 양 측 사람간의 거리의 최솟값이 최대가 되도록 배치할 때 마지막 사람 배치시 양 측 사람간의 거리값을 알아내는 문제입니다.



## IDEA

1. 알아야 할 점은 어느 위치에 배치되냐가 아니라 단순한 거리값입니다.
2. 좌우 값을 구분할 필요 없이 최대 최소만을 알면 됩니다.
3. 좌우 거리 값 중 최소값이 최대가 되게 배치하기 위해서는 둘의 차이가 1 이상 차이나지 않아야 합니다.
4. 이는 사람을 배치하면서 계산되는 거리값은 유사한 값이 중복되어 나오며 이 값은 logn만큼 나옵니다.
5. 최대 거리 값이 중복된 횟수 만큼의 사람 배치는 한 번에 처리할 수 있습니다.(같은 거리값에 사람을 배치할 때 발생하는 좌우 거리값은 항상 똑같기 때문입니다)



## CODE

```c++
/**
* counter : 키->거리값, 값->거리값 갯수
*/
#include <vector>
#include <iostream>
#include <map>
#include <queue>
#include <stdio.h>

#define MIN(x,y)    ((x) < (y) ? (x) : (y))
#define MAX(x,y)    ((x) > (y) ? (x) : (y))

using namespace std;

map<unsigned long long, unsigned long long> counter;
unsigned long long N, K;

int main(){
    int T;

    scanf("%d", &T);
    
    for(int t = 1; t<T+1; t++){
        counter.clear();
        unsigned long long i = 0, lp, rp;

        scanf("%lld %lld", &N, &K);
        
        counter[N] = 1;

        while(i < K){
            /// 가장 큰 거리값을 가져옴. map은 red-black tree
            auto target = prev(counter.end());
            auto key = target->first;
            auto cnt = target->second;

            counter.erase(key);
            i += cnt;
            
            lp = (key-1)/2;
            rp = key-1 - lp;

            counter[lp] += cnt;
            counter[rp] += cnt;
        }
        printf("Case #%d: %lld %lld\n", t, MAX(lp,rp), MIN(lp,rp));
    }
}
```



## 주의 사항

처음에는 힙으로 접근하여 문제를 해결하려 했습니다. 배치하면서 발생하는 좌우 거리의 좌우 좌표를 힙에 유지하여 가장 큰 거리값을 가져오면서 배치를 진행 했습니다. 하지만 이 경우 3 번째 Large Dataset은 처리할 수 없습니다. 배치하는 위치를 알 필요가 없다는 것을 파악하는게 중요했다고 생각합니다. 이를 통해 거리값으로 단순화할 수 있고 중복되는 값을 동시에 처리할 수 있는 것 까지 도달할 수 있게 되는 것 같습니다.