---
layout: post
title: "Oversized Pancake Flipper"
subtitle: "Google Codejam 2017 Qualification Round"
categories: algorithm
tags: codejam
---

## 문제파악

![image](https://user-images.githubusercontent.com/32065940/105710825-fc542380-5f5a-11eb-8846-7904c5a1cc6f.png)



이 문제는 Happy side와 Blank side 양면을 가지는 S개의 pancake이 어느 면이 위에 있는지 상관없이 일렬로 배치되었을 때 연속된 K개의 pancake 면을 뒤집을 수 있는 flipper를 통해 모든 pancake들이 Happy side가 위를 향하게 하도록 뒤지는 최소한의 횟수를 알아내는 것입니다. flipper는 양쪽 끝을 넘어갈 수 없습니다.



## IDEA

1. pancake은 단 두개의 면만을 가지며 두 번 뒤집히는 것은 애초에 뒤집히지 않은 것과 같은 면을 위로 향하게 됩니다. (commutative)
2. 어떤 pancake를 먼저 뒤집든지 순서는 결과에 영향을 끼치지 않습니다.
3. **pancake은 2번 이상 뒤집을 필요가 없다.**
4. flipper는 양쪽 끝을 초과할 수 없기 때문에 **양 쪽 끝을 뒤집을 수 있는 방법은 단 한가지로 정해집니다**.

위를 통해 같은 위치에 flipper를 중복하여 사용할 필요가 없다는 것을 알 수 있습니다. 따라서 small dataset에서는 S가 충분히 작기 때문에 모든 위치에서의 flip을 최소한으로 조합하는 경우를 찾을 수가 있습니다.

large dataset의 경우 S가 커 이러한 방식은 사용할 수 없습니다. 4를 통해 해답을 알 수 있습니다. 만약 최우측의 pancake이 Blank side인 경우 이를 뒤집는 방법은 단 한가지 입니다. 이와 같이 최우측의 뒤집는 것이 결정된 이후의  바로 왼쪽 pancake 또한 이제는 뒤집는 방법이 단 한가지로 정해집니다. 이러한 특성을 활용하여 끝단에서 부터 뒤집는 여부를 결정하여 O(s)의 복잡도로 해결이 가능합니다.(s-k만큼 flip 여부를 결정 후 k개의 pancake이 Happy side인지 확인)



## CODE

```c++
/*
 * pancake : pancake의 side값을 가지는 string 변수
 * K : flipper의 사이즈
 */
#include <iostream>
#include <vector>
#include <string>

std::string pancake;
int K;

/*
 * check : flip_count를 줄여야 되는 위치를 갖는 배열
 * flip_count : 현재 위치에 적용된 flip 횟수
 * result : 총 수행된 flip 횟수
 */
int process(){
    std::vector<int> check(pancake.size()+1, 0);
    int flip_count = 0;
    int result = 0;

    for(int i =0; i<pancake.size()-K+1; i++){
        flip_count -= check[i];
        /// flip_count의 짝홀 여부와 side 값을 통해 flip 여부를 결정
        if(((flip_count & 0x1) && (pancake[i] == '+'))||
            (!(flip_count & 0x1) && (pancake[i] == '-'))){  
            //flip
            result += 1;
            flip_count += 1;
            check[i+K] = 1;
        }
    }
    for(int i= pancake.size()-K+1; i<pancake.size(); i++){
        flip_count -= check[i];
        if(((flip_count & 0x1) && (pancake[i] == '+'))||
            (!(flip_count & 0x1) && (pancake[i] == '-'))){
            //fail
            return -1;
        }
    }
    return result;
}

int main(){
    int T;
    std::cin >> T;
    for(int t = 1; t<T+1; t++){
        std::cin >> pancake;
        std::cin >> K;
        int flip_count = process();
        if(flip_count != -1){
            //success
            std::cout << "Case #" << t << ": " << flip_count << "\n"; 
        }
        else{
            std::cout << "Case #" << t << ": " << "IMPOSSIBLE\n";
        }

    }

}
```

