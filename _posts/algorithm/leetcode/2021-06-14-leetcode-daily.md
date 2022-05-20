---

title: "Leetcode top-interview-question-easy (1~4)"
subtitle: "Leetcode top-interview-question-easy"
categories: algorithm
tags: leetcode
---

## Remove Duplication from Sorted Array

*정수 배열 `nums`가 오름차순(non-decreasing)으로 정렬되있고 중복된 요소들을 in-place에서 제거하라*

2개의 포인터를 유지하여서 문제를 해결할 수 있다.

1. 첫 번째 포인터 **i**는 배열을 검색할 위치를 나타낸다
2. 두 번째 포인터 **j**는 채워야할 배열의 위치를 나타낸다
3. **i**를 증가시키면서 이전 요소와 값이 다르다면 **j** 위치에 값을 복사한다

``` c++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        if(!nums.size())
            return 0;
        int i, j = 0;
        for(i = 1; i<nums.size(); i++){
            if(nums[i] != nums[i-1]){
                nums[++j] = nums[i];
            }
        }
        
        return j+1;
    }
};
```



## Best Time to Buy and Sell Stock 2

*주식의 일자별 가격이 `prices` 배열에 일자를 인덱스로 주어지고 병렬적이지 않은 transaction을 수행하여 만들 수 있는 최대 이익을 계산하라*

다음날 가격이 올라가는 경우의 상승분을 모두 합쳐서 계산할 수 있다

1. *i*th 날의 주가가 *i+1*th 날의 주가 보다 높을 시 *i*th 날에 주가를 사면 안된다. 같은 날에 팔게 되면 주가 낮을 때 산 경우가 더 큰 이익을 내기 때문이다.

2. *i*th 날의 주가가 *i+1*th 날의 주가 보다 낮을 시 *j*th 날에 팔아서 생기는 이익을 비교하면 아래와 같다. 

   > $$ prices[j] - prices[i] == prices[j] - prices[i+1]  - (prices[i+1] - prices[i]) $$

   따라서 상승분을 합친것이 최대 이익을 내는 transaction의 결과와 일치한다

``` c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int result = 0;
        for(int i = 0; i<prices.size()-1; i++){
            if(prices[i+1] > prices[i]){
                //ascending
                result += prices[i+1] - prices[i];
            }
        }
        return result;
    }
};
```



## Rotate Array

*주어진 배열을 `k`만큼 circular-shift 연산을 수행해라*

배열을 3번 뒤집는 연산으로 해결할 수 있다.

1. k가 배열의 길이보다 큰 경우 k%n인 경우와 결과 차이가 없다.
2. shift 연산의 결과는 *k*th 인덱스부터 배열이 시작하는 것으로 볼 수 있다.
3. 배열 전체를 뒤집고 *k*th 인덱스를 기점으로 양 옆 배열을 뒤집어서 만들어 낼 수 있다

``` c++
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        k = k % nums.size();
        reverse(nums.begin(), nums.end());
        reverse(nums.begin(), nums.begin()+k);
        reverse(nums.begin()+k, nums.end());
    }
};
```

> `TODO`: Cyclic Replacement를 이용한 풀이 



## Contains Duplicate

*정수 배열 `nums`에 중복된 값이 존재할 시 참, 없을 시에는 거짓을 반환하라*

정렬을 통해 해결할 수 있다

1. 정렬을 하면 값이 같은 요소들은 인접하게 된다

``` C++
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        for(int i = 1; i< nums.size(); i++){
            if(nums[i-1] == nums[i])
                return true;
        }
        return false;
    }
};
```

