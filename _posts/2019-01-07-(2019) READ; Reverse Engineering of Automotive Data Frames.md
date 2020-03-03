---
layout: post
title: "READ(2019) 논문 리뷰"
subtitile: ""
date: 2019-01-07 22:27:39 +0900
categories: security
tags: ""
---

1. 서론

 해당 논문은 오랜만에 나온 CAN 프로토콜의 데이터 영역 추론 논문이다. 내가 찾은 해외 논문에서는 2번째 논문이다. 과거에 Field Classification ~~ 라는 논문이 있었는데 해당 논문에서도 꽤 필드들의 boundary를 잘 찾아내었다. 다만 이 논문과 차이라면 Counter와 CRC를 찾는지의 여부이다. 과거 논문은 해당 필드들을 식별하지 못하지만 해당 논문은 Counter와 CRC를 식별한다. 

2. 주요 원리

해당 논문에서는 bit flip rate와 Magnitude array를 중요하게 사용된다.
> 여기서 말하는 bit flip rate란 저자는 payload 비트열에서 각각의 비트들이 얼마나 변하는지의 비율이다. 쉽게 말해 0에서 1로변하는 횟수와 1에서 0으로 변하는 횟수를 합한 것에서 전체 패킷의 수로 나눈 것을 말한다.

> Magnitude array는 위에서 언그반 bit flip rate를 이용한다. 단순하게 bit flip rate에서 log 값을 취한 것이라고 생각하면 된다.
해당 논문에서 제시하는 방법에는 2단계를 거쳐 최종 필드들을 식별한다.

- 첫 번째 Phase에서는 Magnitude array만을 가지고 대략적인 field boundary를 결정한다. 방식은 단순하다. 일단 각각의 두개의 비트들만을 잘라서 본다고 생각하면 편하다. 앞에서 부터 순차적으로 2개의 비트열을 끊어서 가져온 후 앞의 비트의 배열값이 뒤의 비트의 배열값보다 큰지를 확인한다. 만약에 앞의 비트의 배열값이 더 크면 그 위치를 field boundary로 식별한다.

- 두 번째 Phase에서는 첫 번째 Phase에서 생성한 boundary array와 bit flip array를 이용하여 최종 field boundary를 도출해낸다. 이 단계에서 Counter와 CRC를 찾아낸다.

- Counter를 식별하는 방식은 간단하다. 우선 the least significant bit의 Magnitude는 0이다(Counter는 1씩 증가하기 때문에 계속 바뀔 수 밖에). 그리고 bit filp rate가 the most significant bit에서 부터 the least significant bit(rate == 1)까지 2배로 증가한다. 이러한 성질을 이용해서 카운터 필드를 식별한다.

- CRC는 어떻게 찾는지 살펴보자. 저자는 실험적으로 통계를 내보니깐 2가지 중요한 결과가 나온다.
>모든 비트의 magnitude 값이 0이다.  
모든 비트의 bit flip rate 값이 0.5를 중앙값으로 하는 정규분포를 따른다.  

3. 결론
- 장점  
 해당 논문은 CRC와 Counter를 추가적으로 찾을 수 있다는 점이 다르다. 각각의 비트가 고정인지 변하는지 여부만을 가지고 필드들을 구분짓는 것에서 더 나아가 변하는 비율을 이용한 것도 큰 차이이다.
- 단점  
 Couter를 1씩 증가하는 것으로만 한정 지었다. 내가 썼던 논문에서 봤듯이 다양한 규칙의 Counter 형식 필드가 존재할 수 있는데 이런 필드는 고려하지 않은 것 같다.    
 CRC외의 다른 Checksum 알고리즘은 식별할 수가 없다. Valasek&Miller 문서를 보면 알 수 있듯이 기존의 알고리즘으로 무차별 대입하여 풀이를 시도했지만 안풀리는 자체 알고리즘을 만들수도 있다. 이러한 경우는 고려하지 않은 것 같다.  
사실 요즘 컴퓨팅 속도가 엄청나게 좋아서 Counter와 CRC만을 찾는 것은 무차별 대입 방식을 사용하면 금방 찾을 수 있을 것이라 예상된다.
