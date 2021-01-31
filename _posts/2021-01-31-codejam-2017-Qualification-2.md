---
layout: post
title: "Tidy Numbers"
categories: algorithm
tags: codejam
---

## 문제파악

 tidy 수란 숫자의 msb 부터 lsb까지 오름차순으로 정렬되있는 수를 말합니다. N이 주어질 때 N 이하의 숫자들 중 가장 큰 tidy 숫자를 찾아야 하는 문제입니다.



## IDEA

1. 주어진 범위내에서 가장 큰 tidy 수를 찾아야 합니다. 가장 큰 값으로 부터 tidy 수를 만든다고 가정할 때 가장 작은 자릿수만을 감소 시켜 tidy 수를 만들어야 합니다.
2. MSB부터 오름차순 여부를 검사하면 처음으로 오름차순이 아닌 자릿수를 찾을 수 있고 이는 최소한 감소 시켜야하는 가장 작은 자릿수가 됩니다.
3. 해당 자릿수에서 1을 감소 시키면 그 다음 자릿수부터는 어떤 수가 나와도 기존 최대값 보다는 무조건 작은수이기 때문에 모든 자릿수가 9가 됩니다.
4. 주의할 점은 해당 자릿수의 1 감소로 인해 이전에 오름차순인 것인 깨질 수 있다는 점입니다.
5. 따라서 첫 번째로 오름차순이 아닌 가장 처음 자릿수를 찾고 1을 감소 시킨후 이전 자릿수와의 오름차순 여부를 확인합니다.
6. 5번의 과정을 MSB까지 반복 진행하여 tidy 수가 되는 순간의 자릿수에서 부터 LSB까지 9로 만들면 가장 큰 tidy 수가 됩니다.

##  CODE

```c++
/*
* numbers : 가장 큰 값
*/
#include <iostream>
#include <string>

std::string numbers;

void process(){
    for(int i = 0; i<numbers.size()-1; i++){
        /// MSB부터 오름차순이 아닌 자릿수 찾기
        if(numbers[i] > numbers[i+1]){
            for(int j = i; j >= 0; j--){
                numbers[j] -= 1;
                /// 1씩 감소시키며 이전 자리수와 오름차순이 되는지 확인
                if(numbers[j-1] <= numbers[j]){
                    /// 오름차순 성립하여 tidy 숫자가 될시 그 다음 자릿수들 다 9
                    for(int k = j+1; k<numbers.size(); k++){
                        numbers[k] = '9';
                    }
                    return;
                }
            }
            numbers[0] -= 1;
            return;
        }
    }
}

int main(){
    int T = 0;
    std::cin >> T;
    for(int t = 1; t< T+1; t++){
        std::cin >> numbers;
        process();

        std::cout << "Case #" << t << ": " << std::stoull(numbers) << "\n";
    }
}
```

