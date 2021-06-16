---
layout: post
title: "자바 ORM 표준 JPA 프로그래밍 - 기본편 (영속성 관리, JPA 내부 구조)"
subtitle: "자바 ORM 표준 JPA 프로그래밍 - 기본편, inflearn"
categories: dev
tags: JPA
---

## JPA에서 가장 중요한 2가지

* 객체와 관계형 DB 매핑
* **영속성 컨텍스트(persistence context)**



## 영속성 컨텍스트(persistence context)

EntityManager가 갖는 논리적인 공간으로 생각할 수 있다. EntityManager와 DB 사이에 위치한다.

> 기본적으로 1 대 1이지만 spring framework와 같은 컨테이너 환경에서는 아니다



### lifecycle

* 비영속(transient) : 영속성 컨텍스트와 관련이 없는 상태. 보통 새로운 객체 생성시 이 상태에 속함
* 영속(managed) : 영속성 컨텍스트에서 관리되는 상태. 보통 persist 메소드로 저장되거나 find 메소드로 조회 했을 때 이 상태에 속함
* 준영속(detached) : 영속 상태에서 분리된 상태. 
* 삭제(removed) : 삭제된 상태

![lifecycle](https://user-images.githubusercontent.com/32065940/122249621-f5c34a00-cf03-11eb-8f29-a260e5c23842.png)



### advantage

1. 1차 캐시 : 영속상태인 Entity들은 모두 1차 캐시에서 유지된다. 따라서 영속상태인 Entity를 find로 조회를 하게 되면 DB 쿼리를 전송하지 않고 캐시에서 바로 가져오게 된다.

2. 동일성 보장 : 같은 primary key를 가진 Entity를 중복 조회시 같은 reference를 갖기 때문에 `==` 연산으로 비교시 true를 반환하게 된다.

3. 트랜잭션을 지원하는 쓰기 지연(transactional write-behind) : 새로운 Entity를 영속상태로 변경시(persist 메소드를 통해 저장 등) 즉각적으로 insert 쿼리를 전송하지 않고 '쓰기 지연 SQL 저장소'에 쿼리를 저장한다. 그 후 `flush`가 될 때 한번에 쿼리가 처리가 된다.

4. 변경 감지(dirty checking) : 영속상태인 Entity를 변경시에 즉각적으로 update 쿼리를 만들지 않고 1차 캐시에 반영을 한다. 1차 캐시에서는 Entity가 처음 영속상태로 변할 당시에 스냅샷을 유지하고 있다가 `flush`되어 DB에 반영되야 하는 순간 바뀐 Entity들을 감지하여 update 쿼리를 만든다.

5. 지연 로딩(lazy loading) : 연관관계가 있는 Entity를 처음부터 join해서 값을 가져오지 않고 실제로 필요한 순간에 쿼리를 전송한다.

   > 나중에 자세히 다룸



### flush

> 영속성 컨텍스트를 DB에 반영
>
> 변경 감지 -> 수정된 Entity 쓰기 지연 SQL 저장소에 등록 ->쓰기 지연 SQL 저장소의 쿼리를 DB에 전송



#### flush 방법

1. em.flush() -> 직접 호출
2. 트랜잭션 커밋 -> 자동 호출
3. JPQL 쿼리 실행 -> 자동 호출(옵션으로 변경 가능)



#### 알아야 할 점

1. 영속성 컨텍스트를 비우지 않는다 -> 1차 캐시 유지
2. 영속성 컨텍스트의 변경내용을 DB에 동기화
3. transaction이라는 작업 단위가 중요 -> 커밋 직전에만 동기화 하면 된다

