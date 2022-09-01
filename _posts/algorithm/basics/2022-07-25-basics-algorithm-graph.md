---
use_math: true
title: "Graph"
subtitle: "Guide to Competitive Programming"
categories: algorithm
tags: basics
---

# Graph  

## Graph 개념 및 용어  
- 그래프:**노드(node 또는 vertex)**와 그들을 잇는 **간선(edge)**으로 구성되어 있다.  
- 경로: 한 노드에서 그래프의 간선을 지나 다른 노드까지 가는 길  
- 경로의 길이: 경로에 포함된 간선의 개수  
- 연결 그래프: 그래프의 모든 노드 간에 경로가 있는 경우  
- 컴포넌트: 그래프의 연결된 부분  
- 트리: 사이클이 없는 연결 그래프  
- 방향 그래프: 간선의 한 방향으로만 이동할 수 있는 그래프  
- 가중 그래프: 간선마다 **가중치**가 존재하는 그래프  
- 이웃(인접) 노드: 두 노드를 잇는 간선이 있는 경우  
- 노드의 차수: 이웃 노드의 개수  
> 그래프의 간선 개수를 m이라고 할 때 차수의 합은 항상 2m 이다. 차수의 합은 항상 짝수이다.  
- 정규 그래프: 모든 노드의 차수가 상수 d로 같은 그래프  
- 완전 그래프: 모든 노드의 차수가 n-1인(모든 두 노드 간에 간선이 있는 경우) 그래프  
- 진입 차수: 방향 그래프에서 그 노드로 향하는 간선의 개수  
- 진출 차수: 방향 그래프에서 그 노드에서 시작하는 간선의 개수  
- 이분 그래프: 모든 노드를 두 가지 색깔 중 하나로 칠하되 이웃 노드의 색깔이 같은 경우가 없도록 만들 수 있는 그래프  
> 이분 그래프인 경우 홀수 개의 간선으로 이루어진 사이클이 없는 경우와 일치한다.  

## 그래프 표현  
1. 인접 리스트  
각 노드에 대해서 출발하는 간선이 있는 노드의 리스트. *일반적으로 사용된다*  
> 무방향 그래프를 저장하는 방식으로 각각의 간선의 양방향에 대해 저장  

2. 인접 행렬  
그래프에 포함된 간선을 나타내는 행렬. 인접 리스트에 비해서 *두 노드 사이에 간선이 있는지 효율적으로 확인 가능*  
> 간선의 유무 뿐만 아니라 가중 그래프에서는 각각의 값이 간선의 가중치를 나타내도록 확장 가능  
- 원소의 개수가 $n^2$이며 그 중 대부분 0으로 채워진다는 단점이 있다  

3. 간선 리스트  
모든 간선을 특정한 순서에 따라 저장한 리스트. *특정한 노드에서 출발하는 간선을 알 필요가 없을 때 편리*  

## 그래프 순회  
노드를 방문하는 순서에 따라 2가지 방법 존재  
### 깊이 우선 탐색(DFS)  
새로운 노드가 발견되는 동안 단일한 경로를 따른다. 그 다음에는 방문했던 이전 노드로 돌아가 그래프의 다른 부분을 탐색한다. 알고리즘을 진행하는 과정에서 방문했던 노드를 기록하므로 각 노드는 한 번씩만 처리된다.  

#### 구현  
재귀(Recursion)을 사용하여 구현할 수 있다.  
- 시간복잡도는 **O(n+m)**으로 노드의 개수(n)과 간선의 개수(m)의 합이다.  
> 모든 노드와 간선을 한 번씩 확인하기 때문  
```C++
void dfs(int s){
    if(visited[s]) return;
    visited[s] = true;
    // 노드 s를 처리
    for(auto u: adj[s]){
        dfs(u);
    }
}
```

### 넓이 우선 탐색(BFS)  
시작 노드에서 각 노드까지의 거리가 증가하는 순서대로 노드를 방문  

#### 구현  
일반적으로 방문할 노드를 관리하기 위한 **큐(Queue)**를 사용하여 단계마다 큐에서 다음노드를 가져와 처리한다.  
- 시간복잡도는 마찬가지로 O(n+m)이다.  
```C++
visited[x] = true;
distance[x] = 0;
q.push(x);
while(!q.empty()){
    int s = q.front(); q.pop();
    // 노드 s 처리
    for(auto u : adj[s]){
        if(visited[u]) continue;
        visited[u] = true;
        distance[u] = distances[s] + 1;// 출발 노드와 거리를 구할 뿐 없어도 무관
        q.push[u];
    }
}
```

### 응용  
#### 연결성 확인  
그래프가 **연결 그래프**인지는 **임의의 노드**에서 출발하여 **다른 모든 노드**를 방문할 수 있는지를 확인하면 된다.  
> 방문하지 않은 노드에 대해 깊이 우선 탐색을 진행하는 과정에서 모든 노드를 방문할 때까지 반복하면 그래프의 모든 컴포넌트를 구할 수 있다.  

#### 사이클 찾기  
그래프 탐색 과정에서 이미 방문했던 노드가 이웃 노드에 포함되어 있다면 사이클이 있다고 판단할 수 있다.  
> 각 컴포넌트에 대해 노드와 간선의 개수를 세는 것도 가능하다.  

#### 이분성 확인  
시작 노드를 X색으로 칠하고 이웃을 Y색으로 칠하고 그 노드의 이웃을 다시 X색으로 칠하는 식의 과정을 반복한다. 이웃한 두 노드가 같은 색깔인 경우가 발견하면 그래프가 이분 그래프가 아니라고 판단할 수 있다.  

### 최단 경로  
그래프에서 두 노드 간의 최단 경로를 구하는 문제  

#### 벨만-포드  
시작 노드에서부터 다른 모든 노드로 가는 최단 경로를 구하는 알고리즘  
- 길이가 음수인 사이클을 포함하지 않는 모든 종류의 그래프를 처리할 수 있다.  
- 그래프에서 음수 사이클이 있는 경우 찾아낼 수 있다.  
> 노드 개수 - 1만큼 단계를 진행했을 때 모든 노드의 최단 거리가 확정되지 않는다면 음수 사이클이 존재한다는 의미  

##### 구현  
> https://www.acmicpc.net/problem/1916  
- Time Complexity: O(V*E)  
```C++
#include <stdio.h>
#include <vector>
#include <cmath>

using namespace std;

#define INF (100000*1000)

int main(){
    int N, M, A, B;
    scanf("%d %d", &N, &M);
    vector<vector<int>> edges(M, vector<int>(3));
    vector<int> dist(N + 1, INF);
    for(int e = 0; e<M; e++){
        scanf("%d %d %d", &edges[e][0], &edges[e][1], &edges[e][2]);
    }
    scanf("%d %d", &A, &B);

    dist[A] = 0;

    for(int n = 0; n<N-1; n++){
        for(int m = 0; m<M; m++){
            dist[edges[m][1]] = min(dist[edges[m][1]], dist[edges[m][0]] + edges[m][2]);
        }
    }

    printf("%d\n", dist[B]);
}
```

#### SPFA
모든 간선을 살펴보는 것이 아닌 정점에 대한 경로의 길이가 변하는 정점에서 출발하는 간선만을 보며 벨만 포드 알고리즘을 개선  

##### 구현  
> https://www.acmicpc.net/problem/1916  
- Time Complexity: O(V*E) 이나 실험적으로 O(E) 값이 나옴  
> https://cstheory.stackexchange.com/questions/51059/proof-of-spfas-worst-case-complexity  
```C++
#include <stdio.h>
#include <vector>
#include <deque>

using namespace std;

#define INF (100000*1000)

int main(){
    int N, M, A, B;
    scanf("%d %d", &N, &M);
    vector<vector<pair<int, int>>> nodes(N+1);
    vector<int> dist(N + 1, INF);
    vector<int> input(3);
    
    for(int e = 0; e<M; e++){
        scanf("%d %d %d", &input[0], &input[1], &input[2]);
        nodes[input[0]].push_back({input[1], input[2]});
    }
    scanf("%d %d", &A, &B);

    dist[A] = 0;
    
    vector<int> check(N+1, 0);
    deque<int> q;
    q.push_back(A);
    check[A] = 1;

    while(!q.empty()){
        int idx = q.front(); q.pop_front();
        check[idx] = 0;
        for(int i = 0; i<nodes[idx].size(); i++){
            if(dist[idx] + nodes[idx][i].second < dist[nodes[idx][i].first]){
                dist[nodes[idx][i].first] = dist[idx] + nodes[idx][i].second;
                if(!check[nodes[idx][i].first]){
                    check[nodes[idx][i].first] = 1;
                    if(!q.empty() && dist[nodes[idx][i].first] < dist[q.front()])
                        q.push_front(nodes[idx][i].first);
                    else
                        q.push_back(nodes[idx][i].first);
                }
            }
        }
    }

    printf("%d\n", dist[B]);
}
```

#### 다익스트라  
벨만 포드와 마찬가지로 특정한 노드에서 모든 노드로 가는 최단 경로를 구하는 알고리즘  
- 장점: 벨만 포드 보다 효율적이다  
- 단점: 가중치가 음수인 간선이 있는 그래프에서는 사용 할 수 없다  
> 거리가 작은 노드를 우선적으로 처리하기 때문에 중복으로 노드를 방문하지 않아 효율적  

##### 구현  
> https://www.acmicpc.net/problem/1753  
- Time Complexity: O(N + MlogM)
```C++
#include <stdio.h>
#include <vector>
#include <queue>

using namespace std;

#define INF (10*20000)

int main(){
    int V, E, S;
    scanf("%d %d %d", &V, &E, &S);
    vector<vector<pair<int, int>>> nodes(V+1, vector<pair<int, int>>());
    vector<int> dist(V+1, INF);
    vector<int> input(3);
    for(int e = 0; e<E; e++){
        scanf("%d %d %d", &input[0], &input[1], &input[2]);
        nodes[input[0]].push_back({input[1], input[2]});
    }

    priority_queue<pair<int, int>> q;
    q.push({0, S});
    dist[S] = 0;

    while (!q.empty()){
        pair<int, int> p = q.top(); q.pop();

        for(int i = 0; i<nodes[p.second].size(); i++){
            if(dist[nodes[p.second][i].first] > dist[p.second] + nodes[p.second][i].second){
                dist[nodes[p.second][i].first] = dist[p.second] + nodes[p.second][i].second;
                q.push({-dist[nodes[p.second][i].first], nodes[p.second][i].first});
            }
        }
    }
    
    for(int v = 1; v<=V; v++){
        if(dist[v] == INF)
            printf("INF\n");
        else
            printf("%d\n", dist[v]);
    }
}
```

#### 플로이드-워셜  
**모든 노드 간 최단 경로**를 구할 수 있는 방식  
여러 라운드로 구성되며 라운드마다 각 경로에서 새로운 중간 노드로 사용할 수 있는 노드를 선택하고 거리를 줄이는 과정을 반복  

##### 구현  
> https://www.acmicpc.net/problem/11404  
- Time Complexity: O(N^3)  
```C++
#include <stdio.h>
#include <vector>
#include <cmath>

using namespace std;

#define INF (100000*100)

int main(){
    int N, M, in[3];
    scanf("%d %d", &N, &M);
    vector<vector<int>> adj(N+1, vector<int>(N+1, INF));
    for(int m = 0; m<M; m++){
        scanf("%d %d %d", &in[0], &in[1], &in[2]);
        adj[in[0]][in[1]] = min(adj[in[0]][in[1]], in[2]);
    }

    for(int i = 1; i<=N; i++){
        adj[i][i] = 0;
    }

    for(int k = 1; k<=N; k++){
        for(int i = 1; i<=N; i++){
            for(int j = 1; j<=N; j++){
                adj[i][j] = min(adj[i][j], adj[i][k] + adj[k][j]);
            }
        }
    }

    for(int i = 1; i<=N; i++){
        for(int j = 1; j<=N; j++){
            if(adj[i][j] == INF)
                printf("0 ");
            else
                printf("%d ", adj[i][j]);
        }
        printf("\n");
    }
}
```

## 사이클 없는 방향 그래프  
그래프의 중요한 분류 중 하나로 사이클이 없는 특성으로 그래프가 DAG라고 가정한다면 문제가 풀기 쉬워지는 경우가 많다. 특히 항상 **위상 정렬**이 가능하며 그 후에 **동적 계획법**을 적용하는 것이 가능하다  

### 위상 정렬  
노드 a에서 노드 b로 가는 경로가 있는 경우 노드 a가 노드 b보다 앞에 나오도록 하는 정렬 방법  
> 방향 그래프에서 위상 정렬이 가능한 경우는 사이클이 없는 경우와 같다.  

#### 구현  
> https://www.acmicpc.net/problem/1005  
```C++
// queue 활용
#include <stdio.h>
#include <vector>
#include <queue>
#include <algorithm>
#include <cmath>

using namespace std;

typedef vector<vector<int>> BUILDING;

#define IND     (0)
#define TIME    (1)
#define ID      (2)

int main(){
    int T, N, K, X, Y, W;
    scanf("%d", &T);
    for(int t = 1; t<=T; t++){
        long long result = 0;
        
        scanf("%d %d", &N, &K);
        vector<int> indegree(N+1, 0);
        vector<int> time(N+1);
        vector<int> dist(N+1, 0);
        vector<vector<int>> next(N+1, vector<int>());
        
        for(int n = 1; n<=N; n++){
            scanf("%d", &time[n]);
            indegree[n] = 0;
        }
        for(int k = 0; k<K; k++){
            scanf("%d %d", &X, &Y);
            indegree[Y] += 1;
            next[X].push_back(Y);
        }
        scanf("%d", &W);

        queue<int> q;
        for(int n = 1; n<=N; n++){
            if(indegree[n] == 0){
                q.push(n);
            }
        }

        while (!q.empty()){
            int temp = q.front();
            q.pop();

            if(temp == W){
                break;
            }

            for(int i = 0; i<next[temp].size(); i++){
                dist[next[temp][i]] = max(dist[next[temp][i]], dist[temp] + time[temp]);
                indegree[next[temp][i]] -= 1;
                if(!indegree[next[temp][i]])
                    q.push(next[temp][i]);
            }
        }
        
        printf("%d\n", dist[W] + time[W]);
    }
    
}
```
```C++
// dfs 활용
#include <stdio.h>
#include <vector>
#include <cmath>

using namespace std;

bool dfs(vector<int> & time, vector<vector<int>> & to, vector<int> & state, vector<int> &arr, int idx){
    if(state[idx] == 1){
        // cycle
        return false;
    }

    state[idx] = 1;

    for(int i = 0; i<to[idx].size(); i++){
        if(state[to[idx][i]] != 2)
            dfs(time, to, state, arr, to[idx][i]);
    }

    state[idx] = 2;
    arr.push_back(idx);

    return true;
}

int main(){
    int T, N, K, W;
    scanf("%d", &T);
    for(int t= 0; t<T; t++){
        scanf("%d %d", &N, &K);
        
        vector<int> time(N+1);
        vector<vector<int>> to(N+1);
        vector<int> in(2);
        vector<int> cost(N+1, 0);
        vector<int> arr;
        vector<int> state(N+1, 0);

        for(int n = 1; n<=N; n++){
            scanf("%d", &time[n]);
            cost[n] = time[n];
        }
        for(int k = 0; k<K; k++){
            scanf("%d %d", &in[0], &in[1]);
            to[in[0]].push_back(in[1]);
        }
        scanf("%d", &W);

        for(int i = 1; i<=N; i++){
            if(state[i])
                continue;
            if(!dfs(time, to, state, arr, i)){
                printf("error\n");
            }            
        }

        for(auto iter = arr.rbegin(); iter != arr.rend(); iter++){
            for(int i = 0; i<to[*iter].size(); i++){
                cost[to[*iter][i]] = max(cost[to[*iter][i]], cost[*iter] + time[to[*iter][i]]);
            }
        }
        
        printf("%d\n", cost[W]);
    }
}
```

### 동적 계획법  
DAG 상에서의 경로와 관련된 많은 질문에 대한 답을 효율적으로 구할 수 있다.  
- 노드 a에서 노드 b로 가는 최단/최장 경로는 무엇인가?
- 서로 다른 경로의 개수는 몇 개인가?
- 경로 중 간선의 개수가 가장 적은/많은 경우 간선은 몇 개인가?
- 모든 경로에 포함된 노드로 어떤 것이 있는가?

## 후속 노드 그래프  
모든 노드의 진출 차수가 1인 그래프로 하나 이상의 컴포넌트로 구성되어 있고 각각은 사이클 하나와 그 사이클로 가는 경로로 구성되어 있다.  
> 함수 f(x) 형태로 후속 노드 그래프의 모든 간선을 표현할 수 있기 때문에 **함수형 그래프**라고도 한다.  

### 후속 노드 구하기  
- succ(x, k) 를 노드 x에서 시작하여 다음 노드로 이동하는 과정을 k번 반복 했을 때 도착하는 노드로 정의하자  
- 단순하게 노드 x에서 출발하여 k번 이동하는 것으로 **O(k)** 시간이 걸리지만 **O(logk)** 시간에도 계산 가능하다  
$$
succ(x,k) = succ(x) \;if \;k = 1 \quad elif \;k > 1\; succ(succ(x, k/2), k/2)
$$
- 위 점화식을 활용하여 $succ(x, 11) = succ(succ(succ(x,8),2),1)$ 과 같이 이미 계산된 값을 가지고서 구할 수 있다  
- 최대 길이를 u 라고 하면 u 보다 작은 2의 제곱수 모든 k에 대한 값을 미리 계산해야 하므로 O(nlogu) 시간이 걸린다  

### 사이클 찾기  
사이클을 찾는 간단한 방법은 그래프에서 이동하면서 방문했던 노드를 모두 기록하는 것이다. 어떤 노드를 두 번째로 방문한다면 그 노드가 사이클의 첫 번째 노드이다.  
> Time complexity: O(N), Space complexity: O(N)  

#### 플로이드 알고리즘  
> Time complexity: O(N), Space complexity: O(1)  
두 개의 포인터 a와 b를 시작노드에서 출발하여 a는 1번 움직일 때 b는 두 번 이동시켜 두 포인터가 만날 때까지 반복한다.  
- 사이클의 길이 : a 는 k 번 이동하고 b는 2k 번 이동했으므로 사이클의 길이는 k의 약수가 되며 a만 이동시켜 다시 만나게 되는 이동 횟수가 길이가 된다.  

## 최소 신장 트리  
- 신장 트리란 모든 노드간에 경로가 존재하는 것을 의미하며 일반적인 트리 처럼 **연결 그래프**이며 **사이클이 없다**  
- 신장 트리의 가중치는 간선 가중치의 합이다.  
- 최소(또는 최대) 신장 트리는 신장 트리 중 가중치가 가장 작은(또는 큰) 것을 의미한다.  
- 그리디 기법을 통해 최소, 최대 신장 트리를 구할 수 있다.  

### 크루스칼 알고리즘  
처음에 그래프의 노드만 포함하고 간선이 하나도 없는 그래프 상태로 시작한 후 가중치 순으로 간선을 포함시키되 사이클이 생기지 않는 간선만 선별하여 추가하는 과정을 반복한다.  
> 각 노드로 시작한 컴포넌트들을 간선이 추가될 때마다 합치는 과정을 거친다.  

#### 증명  
가장 가중치가 작은 간선(a)을 포함하지 않는 최소 신장 트리(t)가 존재한다고 가정을 하자,  
t에서 간선 하나를 제거하고 대신 a를 추가하게 되면 t 보다 가중치가 작은 신장 트리가 만들어진다.  
따라서, 최소 가중치를 가진 간선은 포함되어야 하며 이는 두 번째, 세 번째 최소 가중치 간선들에게도 마찬가지로 적용된다.  

#### 구현  
> https://www.acmicpc.net/problem/1197  
```C++
#include <stdio.h>
#include <vector>
#include <algorithm>

using namespace std;

int uf_find(vector<int> & uf, int c){
    return uf[c] == c ? c : uf[c] = uf_find(uf, uf[c]);
}

void uf_union(vector<int> & uf, int p, int c){
    int pp = uf_find(uf, p);
    int pc = uf_find(uf, c);

    uf[pc] = pp;
}

void uf_init(vector<int> & uf){
    for(int i = 0; i<uf.size(); i++){
        uf[i] = i;
    }
}

int main(){
    int V, E;
    scanf("%d %d", &V, &E);
    vector<vector<int>> edges(E, vector<int>(3));
    for(int e = 0; e<E; e++){
        scanf("%d %d %d", &edges[e][1], &edges[e][2], &edges[e][0]);
    }

    sort(edges.begin(), edges.end());

    vector<int> uf(E+1);
    uf_init(uf);

    long long result = 0;
    for(int e = 0; e<E; e++){
        if(uf_find(uf, edges[e][1]) != uf_find(uf, edges[e][2])){
            result += edges[e][0];
            uf_union(uf, edges[e][1], edges[e][2]);
        }
    }
    printf("%lld\n", result);
}
```

### 유니온-파인드 자료 구조  
집합(서로소 집합)의 묶음을 관리하는 구조, 집합마다 대푯값 원소 하나씩 존재  
> union(두 집합 합치는 연산), find(원소가 포함된 집합의 대푯값 구하는 연산) 모두 시간 복잡도가 O(log n)  
- find: 집합 내 원소에서 대푯값으로 가는 경로가 항상 존재하기 때문에 경로를 따라가면 대표값을 찾을 수 있다.  
- union: 두 집합을 합치기 위해서는 한 집합의 대푯값을 다른 집합의 대푯값으로 이어주면 된다. 이 때 원소의 개수가 작은 집합에서 큰 집합으로 잇게 되면 O(log n)이 되어 보다 효율적  

#### 경로 압축  
```C++
int find(int x){
    return x == link[x] ? x : link[x] = find(link[x]);
}
```
위 함수 연산이 실행이 되면 경로상의 모든 원소가 대푯값을 가르키게 된다.  
> 이에 따른 시간 복잡도는 O(a(n))이 된다고 알려져 있으며 a(n) 은 에커만 함수의 역함수로 **상수**라고 봐도 무방하다. 하지만 *경로 압축을 사용할 수 없는 경우가 존재*한다(ex. 동적 연결성 확인 알고리즘)  

### 프림 알고리즘  
임의의 한 노드를 트리에 추가하고 새로운 노드와 연결하는 간선 중 가중치가 가장 낮은 간선을 선택하며 이 과정을 반복하여 모든 노드를 연결하여 최소 신장 트리를 구한다.  
> 우선 순위 큐를 활용하여 간선을 선택하면 시간 복잡도는 O(n + m log m)  

* 크루스칼 알고리즘과 프림 알고리즘은 모두 효율적이면 성향에 따라 선택 가능하다  