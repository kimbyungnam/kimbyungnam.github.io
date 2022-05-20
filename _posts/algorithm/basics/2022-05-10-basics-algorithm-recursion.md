---
title: "Recursion"
subtitle: "Guide to Competitive Programming"
categories: algorithm
tags: basics
---

# recursion  
## Create subset  
원소가 n개인 집합의 모든 부분집합을 생성
> {1,2,3} -> 0, {1}, {2}, {3}, {1,2}, {1,3}, {2,3}, {1,2,3}

```c++
#include <stdio.h>
#include <vector>

using namespace std;

void serach(vector<int> & subset, int k){
    if(k==n+1){
        for(auto s : subset){
            printf("%d ", s);
        }
        printf("\n");
    }
    else{
        // k를 부분집합에 포함
        subset.push_back(k);
        search(k+1);
        subset.pop_back();
        // k를 부분집합에 포함시키지 않는다
        search(k+1);
    }
}
```

## Create permutation  
원소가 n개인 집합의 모든 순열을 생성  
> {1,2,3} -> {1,2,3}, {1,3,2}, {2,1,3}, {2,3,1}, {3,1,2}, {3,2,1}  

```c++
#include <stdio.h>
#include <vector>

using namespace std;

void search(vector<int> & permutation, vector<int> & chosen){
    if(permutation.size() == n){
        for(auto p : permutation){
            printf("%d ", p);
        }
        printf("\n");
    }
    else{
        for(int i = 1; i<=n; i++){
            if(chosen[i])   continue;
            chosen[i] = true;
            permutation.push_back(i);
            search(permutation, chosen);
            chosen[i] = false;
            permutation.pop_back();
        }
    }
}
```

## Backtracking  
퇴각검색(Backtracking)은 **비어 있는 해로 탐색을 시작**하고, 단계마다 해를 **확장**해 나가는 방식의 알고리즘. 탐색 과정에서 해를 생성하는 모든 방법을 재귀적으로 하나하나 확인하게 된다.  

### n queen problem  
n*n 배열에서 n개의 퀸을 서로 공격할 수 없도록(대각선, 가로, 세로 겹치지 않게) 배치하는 방법의 수 구하기
```c++
#include <vector>

using namespace std;

void search(int y, vector<int> & col, vector<int> & diag1, vector<int> & diag2){
    if(y == n){
        count++;
        return;
    }

    for(int x = 0; x<n; x++){
        if(col[x] || diag1[x+y] || diag2[x-y+n-1])  continue;
        col[x] = diag1[x+y] = diag2[x-y+n-1] = 1;
        search(y+1);
        col[x] = diag1[x+y] = diag2[x-y+n-1] = 0;
    }
}
```  
