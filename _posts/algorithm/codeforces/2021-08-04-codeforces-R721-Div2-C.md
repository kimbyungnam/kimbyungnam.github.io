---

title: "MEX Tree (#D)"
subtitle: "Codeforces Round 721 Div.2"
categories: algorithm
tags: codeforces
---

## 문제파악

vertex 번호가 0 부터 시작하여 N-1 까지 있는 트리에서 MEX 값이 0 부터 N 까지 를 이루는 경로의 개수를 계산해라



## IDEA

1.  부분트리의 사이즈(노드의 갯수)는 DP를 통해서 O(N) 의 시간 복잡도로 구할 수 있다.

   > #CODE 의 dfs_cal_sub_size 참고

2. $MEX(i) = MEX(>=i) - MEX(>i)$​ 라는 수식이 성립한다

3. MEX(>i) 는 0 부터 i-1 까지 모든 노드를 경로에 포함하고 있어야만 한다.

4. i가 0인 경우를 생각해보자

   > $MEX(0)  = MEX(>= 0) - MEX(>0)$, 여기서 $MEX(>=0)$은 트리에서 만들 수 있는 모든 경로의 갯수($N(N-1)/2$)가 되고 $MEX(>0) = MEX(>=0) - MEX(0)$이 되는데 $MEX(0)$은 0을 포함하지 않는 모든 경로의 개수로 0을 루트로 했을 경우 자식 노드를 루트로 하는 트리들로 만들 수 있는 경로의 수의 합이 된다.

5. i가 1인 경우를 생각해보자

   > $MEX(1) = MEX(>=1) - MEX(>1)$, 여기서 $MEX(>=1) 은 MEX(>0) $과 같으며 2번에서 이미 구할 수 있었다. 그리고 MEX(>1)은 0을 거쳐가는 모든 경로의 개수가 된다.

6. 3,4에 따라서 i가 3인 경우에서도 MEX(>=3)는 이미 값을 알고 있으며 MEX(>3)는 0과 1 그리고 2를 포함하는 모든 경로의 개수가 된다

   > 0, 1, 2 그리고 점점 경로에서 포함해야 하는 것들을 늘려가는데 이런 확장은 경로의 양 끝단에서만 가능하다

7. 특정 경로를 포함하는 경로의 갯수를 구하기 위해서는 양 끝단을 루트로 하는 부분트리의 갯수를 곱하면 된다

8. 만약 i가 이미 이전에 경로를 구성할 때 포함되어 있다면 $MEX(>=i)와 MEX(>i)$는 서로 같게 된다

   


## CODE

```c++
#include <stdio.h>

#include <vector>
#include <set>

#define CAL_SIZE(x) (((x)*(x-1))/2)

using namespace std;

typedef long long ll;

ll cal_size(ll x){
    return CAL_SIZE(x);
}

void dfs_cal_sub_size(int root, int parent, vector<vector<int>>& adj, vector<ll>& sizes){
    for(auto v : adj[root]){
        if(v == parent)
            continue;
        dfs_cal_sub_size(v, root, adj, sizes);

        sizes[root] += sizes[v];
    }

    sizes[root] += 1;
}

int dfs_find_node(int node, int* edge, set<int>& check, vector<vector<int>>& adj, vector<ll>& sizes){
    int saved_edge = *edge;
    for(auto v : adj[*edge]){
        if(check.count(v))
            continue;
        check.insert(v);
        *edge = v;
        sizes[saved_edge] -= sizes[v];
        if(v == node){
            return 1;
        }
        if(dfs_find_node(node, edge, check, adj, sizes)){
            return 1;
        }
        check.erase(v);
        *edge = saved_edge;
        sizes[saved_edge] += sizes[v];
    }

    return 0;
}

int main(){
    int T, u, v;
    ll N;
    scanf("%d", &T);

    for(int t=0; t<T; t++){
        scanf("%lld", &N);
        vector<ll> sizes(N, 0);
        vector<vector<int>> adj(N, vector<int>());
        set<int> check;
        vector<ll> mex(N+1, 0);
        int l, r;
        

        for(int n = 0; n<N-1; n++){
            scanf("%d %d", &u, &v);
            adj[u].push_back(v);
            adj[v].push_back(u);
        }

        // calculate subtree size
        dfs_cal_sub_size(0, 0, adj, sizes);

        check.insert(0);
        l = r = 0;
        for(auto v : adj[0]){
            if(v  == 0)
                continue;
            mex[0] += CAL_SIZE(sizes[v]); 
        }
        ll prev = CAL_SIZE(N) - mex[0];

        int n;
        for(n = 1; n<N+1; n++){
            if(check.count(n)){
                mex[n] = 0;
                continue;
            }

            if(!dfs_find_node(n, &l, check, adj, sizes)){
                if(!dfs_find_node(n, &r, check, adj, sizes)){
                    // fail
                    mex[n] = prev;
                    break;
                }
            }
            mex[n] = prev;
            prev = sizes[l] * sizes[r];
            mex[n] -= prev;
        }
        for(auto m : mex){
            printf("%lld ", m);
        }
        printf("\n");
    }
}
```



## 주의 사항

1. MEX에 대한 식을 생각해내는 것이 중요하다
2. 간단한 테스트 케이스를 직접 해보는 것이 아이디어를 떠올리는데 도움이 된다
3. 아이디어가 다소 복잡하다보니 코딩전에 확실히 잡고 코딩을 시작해야 좋다
4. 트리에 대한 기본적인 알고리즘들을 알고 있으면 좋다
