---
layout: post
title: "openssl DRBG(NIST sp800-90A) 분석"
categories: security
tags: openssl
---

(미완)

## DRBG

Deterministic Random Bit Generator의 축약어로 NIST에서 sp800-90A 표준인 유사난수를 생성하는 방법입니다. 난수 생성간에 Hash, hmac, AES, TDES를 사용할 수 있습니다.

![image](https://user-images.githubusercontent.com/32065940/107867463-5f800880-6ebe-11eb-96e0-94ec92b4b76a.png)

위의 그림은 DRBG 기능 모델을 보여줍니다. 기본적으로 Internal State를 가지고 난수를 반복적으로 생성하며 또한 Internal State 업데이트를 수행합니다. 초기 Internal State는 난수(Entropy)와 옵션 값(Nonce, Personalization String)을 활용하여 seed를 생성하여 업데이트를 하며 사용자가 원할 시 Internal State는 언제든지 Reseed 함수를 통해 다시 seed를 생성, 업데이트 가능합니다.



## Openssl

본 글에서는 OpenSSl-v1.1.1을 기준으로 AES를 이용한 CTR-DRBG 위주로 서술합니다.

 ### 사용법

```

```



