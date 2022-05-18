---
layout: post
title: "Leetcode top-interview-question-easy (5~8)"
subtitle: "Leetcode top-interview-question-easy"
categories: algorithm
tags: leetcode
---

## Single Number

*비어있지 않은 정수 배열이 단 한개의 요소를 제외하고 나머지 요소가 모두 배열에서 2번 나타날 때 한 번만 나타나는 한 개의 요소를 찾아라*

### time: O(N), space: O(N)

Hash table을 사용하여 해결한다.

1. 배열을 순회하면서 해쉬 테이블에 갯수를 업데이트 한다
2. 해쉬 테이블에서 갯수가 1인 키를 찾는다

### time: O(N), space: O(1)

`XOR` 연산의 특성을 활용한다.

1. XOR 연산은 교환 법칙이 성립된다.
2. 같은 수를 XOR 연산할 시 결과는 0이 된다.
3. 배열의 모든 요소를 XOR하게 되면 2번 나타나는 값들을 다 0이 되고 한 번만 나오는 값만이 남게 된다

``` c++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        for(int i = nums.size() - 1; i > 0; i--){
            nums[i-1] ^= nums[i];
        }
        return nums[0];
    }
};
```



## Intersection of Two Arrays 2

*두 개의 정수 배열이 주어질 때 두 배열에 모두 존재하는 요소들을 순서에 상관없이 반환해라*

### time: O(N+M), space(N)

Hash table을 사용하여 해결한다.

1. nums1을 순회하여 hash table의 각 요소들의 값의 갯수를 저장한다.
2. nums2를 순회하며 hash table을 조회하고 1이상일시 table의 값을 하나 줄임과 동시에 결과 배열에 값을 추가한다.

``` C++
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        unordered_map<int, int> c1;
        for(int i = 0; i< nums1.size(); i++){
            c1[nums1[i]]++;
        }
        vector<int> result;
        for(int i =0; i< nums2.size(); i++){
            if(c1[nums2[i]]){
                result.push_back(nums2[i]);
                c1[nums2[i]]--;
            }
        }
        return result;
    }
};
```

### Question 1

*배열이 이미 정렬된 상태인 경우 최적화를 해라*

#### time: O(N+M), space(1)

두 개의 포인터를 사용하여 해결할 수 있다.

1. 각 배열의 시작에 포인터를 각각 위치한다.
2. 값이 같은 경우 두 포인터 모두 증가한다.
3. 다를 경우 작은 값을 가지는 포인터만 증가한다.

``` C++
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        sort(nums1.begin(), nums1.end());// 정렬된 상태를 만들어주는 것
        sort(nums2.begin(), nums2.end());// 정렬된 상태를 만들어주는 것
        
        vector<int> result;
        
        int i = 0, j = 0;
        while(i<nums1.size() && j<nums2.size()){
            if(nums1[i] == nums2[j]){
                result.push_back(nums1[i]);
                i++; j++;
            }
            else if(nums1[i] < nums2[j]){
                i++;
            }
            else{
                j++;
            }
        }
        return result;
    }
};
```

### Question 2

*nums1이 nums2에 비해 상대적으로 길이가 작은 경우 최적화를 해라*

Hash table을 nums1을 기준으로 구성하여 space complexity를 다소 줄일 수 있다.

> 최선인지는 잘 모르겠다

### Question 3

*nums2가 너무 커서 메모리에 한 번에 다 올릴 수 없을 경우에 해결해라*

메모리에 로드 가능한 사이즈를 기준으로 블락별로 정렬을 한후 각 블락별로 포인터를 이용하여 external sort를 수행하고 *Question 1*의 전략을 사용한다.



## Plus One

*배열의 요소가 각 10진법의 수를 나타낼 때 더하기 1을 한 결과를 배열로 반환해라*

### time: O(N), space: O(1)

1의 자리를 나타내는 배열의 요소부터 1을 더하고 캐리 값을 그 다음 자리로 넘기는 방식으로 해결한다.

> 캐리가 발생하지 않을 경우 즉각 결과를 반환하여 조금 더 최적화가 될 수 있다.
>
> 아래 코드에서는 배열을 뒤집기 대신에 insert를 통해 맨 앞에 추가하는 방식으로 진행할 수 있다.

``` C++
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
        int carry = 1;
        vector<int> result;
        for(auto iter = digits.rbegin(); iter != digits.rend(); iter++){
            int temp = carry + *iter;
            carry = temp/10;
            result.push_back(temp%10);
        }
        if(carry)
            result.push_back(carry);
        reverse(result.begin(), result.end());
        
        return result;
    }
};
```



## Move Zeros

*정수 배열에서 모든 0을 가장 끝으로 이동시켜라(in-place)*

### time: O(N), space: O(1)

2 개의 포인터를 활용하여 해결할 수 있다.

1. 0의 위치를 나타낼 포인터와 배열을 순회할 포인터를 둔다.
2. 0이 아닌 값을 포인터가 가르킬 때 두 포인터가 가르키는 값들을 바꾼다.

``` C++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        for(int i=0, j=0; i<nums.size(); i++){
            if(nums[i] != 0)
                swap(nums[i], nums[j++]);
        }
    }
};
```

