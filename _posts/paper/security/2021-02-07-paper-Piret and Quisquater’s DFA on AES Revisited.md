---

title: "(2010)Piret and Quisquater’s DFA on AES Revisited 논문 리뷰"
subtitile: "AES DFA 논문"
categories: paper
tags: security
---

## 서론

이 논문은 AES와 KHAZAD를 대상으로 매우 적은 개수의 오류 주입으로 키를 복원하는 공격을 수행한 기존 공격을 AES에 한해서 보다 좋은 효율로 공격을 수행하는 방법을 서술합니다.

논문에서는 AES-128,192,256을 모두 포함하지만 본 글에서는 AES-128을 대상으로 한 공격만을 다룹니다.

## 주요 원리

#### previous work

1. AES의 마지막 MixColumn의 입력값에 오류가 발생했다면 4byte의 위치에 오류가 확산되있습니다. 오류가 발생한 열의 다른 바이트에 오류가 발생했을 경우에도 마찬가지로 같은 위치의 4byte로 오류가 확산됩니다.

   ![image](https://user-images.githubusercontent.com/32065940/107137899-18d95e00-6954-11eb-89a0-63d320aaf462.png)

   오류가 확산된 4바이트의 모든 경우의 수는 $255^4$(256 - 정상 입력값 1가지) 이지만 위의 성질을 이용하여 한바이트의 오류로 발생할 수 있는 경우의 수는 $255*4$가 되며 후보키가 될 수 있는 확률은 아래와 같습니다.
   $$
   255*4 \over 255^4
   $$
   이 확률에 의해 모든 키($256^4$가지)를 테스트할 경우 존재하는 후보키는 1036가지 입니다 ($256^4 * {255*4 \over 255^4} \approx 1036$) .

2. 만약 오류가 주입된 경우가 2개인 경우에는 아래와 같이 후보키가 될 수 있는 확률이 극히 작아져 높은 확률로 단일개의 후보키만이 남게됩니다.
   $$
   256^4 * {255*4 \over 255^4} * {255*4 \over 255^4}\approx 2.49× {1 \over 10^4}
   $$

#### 제안하는 향상된 공격

![image](https://user-images.githubusercontent.com/32065940/107137594-525c9a00-6951-11eb-8e1a-405b0b64fcbd.png)

1. AES에서는 연산과정을 크게 AddRoundkey, SubByte, ShiftRow, MixColumn이 반복수행되며 이루어집니다. 만약 연산과정에서 1 byte의 오류가 발생했을 경우 AddRoundkey와 SubByte 그리고 ShiftRow 연산의 경우 해당 바이트만 영향을 받고 나머지 바이트로 확산(diffusion)되지 않는다. 반면에 MixColumn의 경우 해당 열, 즉 총 4바이트로 오류가 확산됩니다. 이렇게 확산된 오류가 ShiftRow 연산을 수행하게 되면 각각 다른 열로 옮겨지게 되고 이후 MixColumn을 수행하면 모든 바이트로 오류가 확산되게 됩니다. 

   > 이는 곧 16 바이트의 모든 키를 찾기 위해서 9라운드의 오류를 총 8 번 발생시키는 대신 8라운드에 오류를 2번만 발생 시켜도 기존 공격을 수행할 수 있게 됩니다. 

2. 오류가 발생한 열 위치를 알고 있다면 ShiftRow의 결과값에서 몇 행 몇 열의 위치에 오류가 존재하는지 식별할 수 있습니다. 이는 곧 한 열에서 모든 행에 대한 오류를 테스트하는 대신에 오로지 알고 있는 위치의 오류에 경우만을 테스트함으로써 확률을 더 줄일 수가 있습니다. 따라서 아래와 같이 $2^8$의 후보키가 남게 됩니다.
   $$
   256^4 * {255*4 \over 255^4} \approx 259 \approx 2^8
   $$

   > 모든 키를 복원하기 위해서는 결국 $2^{8^4} = 2^{32}$ 의 후보키가 남게됩니다.

3. 오류의 위치를 알 수 없는 경우에서는 ShiftRow의 결과의 경우의 수가 총 4가지이므로 후보키를 테스트할 경우 남은 후보키의 경우의 수는 최종적으로 $2^{32}*4 = 2^{34}$가 됩니다.

   

## 결론

![image](https://user-images.githubusercontent.com/32065940/107139081-c7cd6800-695b-11eb-83b6-d322762b73e1.png)

AES의 확산되는 원리를 이용하여 8과 9 라운드 사이가 아닌 7과 8라운드 사이의 오류를 통해 복원을 위해 필요한 오류 주입 결과의 수를 4분의 1로 줄일 수 있었습니다. 또한 ShiftRow로 오류가 옮겨지는 경우가 한정되 있다는 점을 활용해 모든 위치에서의 오류를 찾는 대신 한정된 위치만을 확인하여 경우의 수를 줄일 수 있었습니다.



## 참조

[Piret and Quisquater’s DFA on AES Revisited(iacr.org)](https://eprint.iacr.org/2010/440.pdf)

[A Differential Fault Attack Technique against SPN Structures, with Application to the AES and Khazad (researchgate.net)](https://www.researchgate.net/publication/221291688_A_Differential_Fault_Attack_Technique_against_SPN_Structures_with_Application_to_the_AES_and_Khazad) : previous work

