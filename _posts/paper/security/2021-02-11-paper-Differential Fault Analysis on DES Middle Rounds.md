---
use_math: true
title: "(2009)Differential Fault Analysis on DES Middle Rounds 논문 리뷰"
subtitile: "DES DFA 논문"
categories: paper
tags: security

---

(미완)

## 서론

이 논문은 DES에서 기존 오직 13, 14, 15 라운드에 주입한 오류만이 공격 대상인 제한을 벗어나고 12, 11, 10.. 등 더 상위 라운드에서의 오류 주입 공격을 서술합니다. 



![image](https://user-images.githubusercontent.com/32065940/107596067-e3858680-6c59-11eb-9491-3865ecd5d260.png)
$$
f_i(R_{15}, k_{16,i}) ⊕ f_i(\tilde{R_{15}}, k_{16,i}) = (R_{16} ⊕ \tilde{R_{16}})_i ⊕ (L_{15} ⊕ \tilde{L_{15}})_i
$$
위의 그림과 식을 이해하는 것이 중요합니다. 위의 식은 오류가 주입된 라운드와 상관없이 항상 일치하게 되는 수식입니다. 하지만 $R_{15} == L_{16}, \tilde{R_{15}} == \tilde{L_{16}}$은 알 수 있는 값인 반면 $L_{15}, \tilde{L_{15}}$은 알 수 없어 키를 구할 수 없습니다. 여기서 white box와 black box 공격 방식에서 접근 방법에 차이가 생길 수 있습니다. white box 기반 공격이라면 이전 라운드 값( $L_{15}, \tilde{L_{15}}$)을 통해 이 수식에서 키 값을 복원할 수 있으며 이는 어떤 라운드인지 상관없이 공통적으로 적용됩니다. black box인 경우 이 논문에서 제안하는 wrong key distinguisher를 통해 확률적으로 키 값을 복원할 수 있게 됩니다. 



## 주요 원리

$$
g_i(k) = f_i(R_{15},k)⊕ f_i(\tilde{R_{15}},k) ⊕ (R_{16}⊕\tilde{R_{16}})
$$

위의 $g_i(k)$는 guess 함수입니다. 만약 k가 알맞은 키 값이라면 $g_i(k) = (L_{15}⊕\tilde{L_{15}})$가 됩니다. 반면에 키 값이 아니라면 $g_i(k) \sim u(\left\{0,1 \right\}^4)$이 됩니다.

> 키 값이 일치 한다면 guess 함수의 결과값은 편향된다는 의미입니다.

