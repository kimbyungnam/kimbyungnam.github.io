---
layout: post
title: "Boring Segments (#E)"
subtitle: "Codeforces Educational Round 112 Div.2"
categories: algorithm
tags: codeforces
---

## 문제파악

전체 구간의 여러 segment 들이 주어지고 각 segment 별로 weight가 있을 때 전체 구간을 포함하도록 segment들을 선택하면서 weight의 최대값과 최소값의 차이의 최소값을 구해라



## IDEA

1. 모든 segment 를 탐색할 수는 없다

2. 최소 또는 최대값 하나만 필요하다면 Binary search가 가능하지만 두 값의 차이가 최소가 되어야 하기 때문에 불가능하다

3. weight이 제일 작은 segment를 포함하고 나머지 segment 들을 선택했을 때의 weight(w1)의 최대값은 weight이 제일 작은 segment를 제외하고 나머지 segment들로만 선택을 했을 경우의 weight(w2)의 최대값을 비교하면 w2는 w1보다 크거나 작다

   > weight의 최소값을 증가시키면 최대값은 같거나 더 증가만 할 수 있다

4. 3을 통해서 two pointer 접근을 하였을 때 time complexity가 크지 않아짐을 알 수 있다

   > 최소와 최대 pointer 모두 초기값 이후에는 증가하는 방향으로만 조정되기 때문에

5. segment 들을 weight 순으로 정렬을 한 후 two pointer를 적용할 수 있다

   > 전체 구간을 포함했는지 여부 확인을 위해서 segment tree 자료구조를 활용할 수 있다




## CODE

```c++
#include <stdio.h>
#include <vector>
#include <algorithm>

#define MIN(x, y) ((x) < (y) ? (x) : (y))

using namespace std;

typedef struct NODE{
    int l, r, w;
} NODE;

int min_func(int lp, int rp){
    return MIN(lp, rp);
}

void update_lazy(vector<int>& tree, vector<int>& lazy, int idx){
    if(idx*2 + 1< tree.size()){
        lazy[idx*2] += lazy[idx];
        lazy[idx*2 + 1] += lazy[idx];
    }
    
    tree[idx] += lazy[idx];
    lazy[idx] = 0;
}

void update_interval(vector<int> &tree, vector<int>& lazy, int idx, int s, int e, int qs, int qe, int data){
    update_lazy(tree, lazy, idx);
    if(qs > e || qe < s)
        return;
    if(qs <= s && qe >= e){
        tree[idx] += data;
        if(s != e){
            lazy[idx*2] += data;
            lazy[idx*2+1] += data;
        }
        return;
    }

    int m = (s+e)/2;
    
    update_interval(tree, lazy, idx*2, s, m, qs, qe, data);
    update_interval(tree, lazy, idx*2+1, m+1, e, qs, qe, data);

    tree[idx] = min_func(tree[idx*2], tree[idx*2+1]);
}

int query_data(vector<int> &tree, vector<int>& lazy, int idx, int s, int e, int qs, int qe){
    update_lazy(tree, lazy, idx);
    if(qs > e || qe < s)
        return 1;
    if(qs <= s && qe >= e){
        // printf("[%d : %d] = %d\n", s, e, tree[idx]);
        return tree[idx];
    }

    int m = (s+e)/2;
    return min_func(query_data(tree, lazy, idx*2, s, m, qs, qe), query_data(tree, lazy, idx*2+1, m+1, e, qs, qe));
}

bool segment_cmp(NODE lp, NODE rp){
    return lp.w < rp.w;
}

int main(){
    int N, M, l, r, w, answer = __INT32_MAX__;

    scanf("%d %d", &N, &M);

    vector<NODE> segments;
    vector<int> seg_tree(4*M, 0);
    vector<int> lazy(4*M, 0);

    for(int n = 0; n<N; n++){
        scanf("%d %d %d", &l, &r, &w);
        segments.push_back({l,r,w});
    }

    sort(segments.begin(), segments.end(), segment_cmp);

    int j = 0;
    for(int n = 0; n<N; n++){
        while(j < N && !query_data(seg_tree, lazy, 1, 1, M, 1, M-1)){
            update_interval(seg_tree, lazy, 1, 1, M, segments[j].l, segments[j].r - 1, 1);
            j++;
        }
        if(!query_data(seg_tree, lazy, 1, 1, M, 1, M-1)){
            break;
        }
        answer = min_func(answer, segments[j - 1].w - segments[n].w);
        update_interval(seg_tree, lazy, 1, 1, M, segments[n].l, segments[n].r - 1, -1);
    }

    printf("%d\n", answer);
}
```



## 주의 사항

1. 최소와 최대 값이 증가하는 방향만이 가능하다는 것을 파악하는 것이 중요하다
2. segment를 구간의 위치 대신 weight 을 중점으로 정렬하는 것이 중요하다
3. segment tree 활용시 구간 업데이트와 쿼리가 많은 경우 lazy propagation 을 접목해야 하는 경우가 많다

