---
layout: post
title: "리버싱 바이블 - 1. 리버스 엔지니어링 기본"
categories: security
tags: reversing             
        

---

# 리버스 엔지니어링 기본

#### 어셈블리의 명령 포맷

이 책은 IA-32 구조를 기본으로 서술한다. IA-32의 기본 형태는 단순하다

`명령어(opcode) + 인자(operand)`

> 모든 인자(operand)는 앞의 것이 destination이 되고 뒤의 것이 source가 된다.



#### 레지스터

레지스터는 간단히 말하면 CPU가 사용하는 `변수`이다. 레지스터는 **EAX**, **EBX**, **ECX**, **ESI**, **EDI**, **EBP**, **ESP**로 총 8개가 있다. 

* EAX

  A가 accumulator를 나타내며 `산술 계산을 하며 리턴값을 전달`한다.

* EDX

  D가 Data를 나타내며 EAX와 역할은 같으나 **리턴값으로는 사용되지 않는다**.

* ECX

  C는 Count를 나타내며 `반복문을 수행할 때 카운팅`하는 역할을 한다. 카운팅이 필요 없을 시에는 마찬가지로 변수로 사용해도 무방하다. 일반적으로 생각하는 0부터 증가하는 방식이 아닌 미리 루프를 돌 값을 넣어놓고 감소시키며 반복문이 진행된다.

* EBX

  위의 레지스터가 부족할 때 사용된다. 그냥 변수이다.

* ESI, EDI

  ESI는 문자열이나 각종 반복 데이터를 처리 또는 메모리를 옮기는데 사용된다. 보통 **ESI는 시작 인덱스, EDI는 목적지 인덱스**로 사용된다. 그리고 경우에 따라 al, ah 등의 레지스터는 16비트 레지스터로 크기가 반 정도 작고 위의 레지스터의 **일부분**만을 사용하는 레지스터가 된다. 아래 사진을 참고하자.

![image](https://user-images.githubusercontent.com/32065940/76166897-8fe6ff00-61a5-11ea-8b27-77d598c4d146.png)

> 어셈블리에서 연산은 레지스터를 이용한다. 메모리끼리는 연산할 수가 없다. 메모리의 값을 연산에 활용하기 위해서는 레지스터에 담고 연산을 한후 다시 메모리로 담아야 한다.



#### 어셈블리 명령어

어셈블리 명령어는 종류가 매우 다양하고 아키텍처마다 쓰는 것이 다르다. 이를 다 외우는 것은 불가능이라고 한다. 필요할 때 찾아가는 방식을 택하고 외워야 하는 것은 기본적인 것들이다.

* PUSH, POP

  스택에 값을 넣고 빼는 명령어

* MOV

  값을 단순히 넣는 명령어

* LEA

  MOV와 비슷하지만 이는 변수의 값이 아닌 주소를 가져오는 명령어

* ADD, SUB

  값을 더하기, 빼기 연산하는 명령어

* INT

  인터럽트를 일으키는 명령어

* CALL

  함수를 호출하는 명령어

* INC, DEC

  값을 1 증가, 감소하는 명령어

* AND, OR, XOR

  비트 연산하는 명령어

* NOP

  아무것도 하지 말라는 명령어

* CMP, JMP

  비교, 점프하는 명령어



#### 리버싱에 필요한 스택

1. 함수 호출 시 파라미터가 들어가는 방향
2. 리턴 주소
3. 지역 변수 사용



#### 함수의 호출

함수의 인자는 스택에 값을 `LIFO` 순서대로 넣기 때문에 실제 소스 코드에서 호출한 것과는 반대로 들어간다. 함수를 호출할 때 스택에 파라미터들을 넣어놓기 때문에 이 값들에 접근하기 위해서는 `ebp에서 오프셋을 더하는 방식`으로 계산해야 한다. ebp+8에는 첫 파라미터가 담겨 있다.



#### 리턴 주소

`ebp+4`에는 리턴 주소가 담긴다. 


