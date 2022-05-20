---

title: "자바 ORM 표준 JPA 프로그래밍 - 기본편 (엔티티 매핑, 연관관계 매핑)"
subtitle: "자바 ORM 표준 JPA 프로그래밍 - 기본편, inflearn"
categories: dev
tags: JPA
---

# Entity mapping

객체와 테이블을 연결하는 방법 -> annotation 활용



## @Entity

JPA가 관리할 클래스를 지정하는 annotation

필수로 작성해야 하는 부분

### 주의

> 기본 생성자 필요 -> JPA 동작시 필요
>
> final은 저장되지 않음
>
> final 클래스, enum, interface, inner 클래스는 사용 안됨



## 데이터베이스 스키마 자동 생성

애플리케이션 실행 시 DDL을 자동생성 가능 -> DB 방언을 활용해 각각 적절한 DDL 생성 가능

### 주의

> 운영시 기능을 제한해야 한다. -> 운영DB가 날라갈 가능성 존재



### 속성

| 옵션        | 설명                                       |
| ----------- | ------------------------------------------ |
| create      | 기존 테이블 삭제 후 다시 생성(DROP+CREATE) |
| create-drop | create와 같으나 종료시점에 테이블 DROP     |
| update      | 변경되는 사항만 alter                      |
| validate    | 엔티티와 테이블이 정상 매핑되었는지만 확인 |
| none        | 사용하지 않음                              |



### @Column

제약조건을 추가 가능

> ex) @Column(nullable=false, length =10)

DDL 생성 기능은 DDL 자동 생성시에만 영향을 끼치고 `JPA 실행 로직에는 영향이 없다.`



## 필드와 컬럼 매핑

| 어노테이션  | 설명                                           |
| ----------- | ---------------------------------------------- |
| @Column     | 컬럼 매핑                                      |
| @Temporal   | 날짜 타입 매핑(최신 hibernate에서는 생략 가능) |
| @Enumerated | enum 타입 매핑                                 |
| @Lob        | BLOB, CLOB 매핑                                |
| @Transient  | 특정 필드를 컬럼에 매핑하지 않음(매핑 무시)    |



### 주의

자바 enum 타입을 매핑시 `ORDINAL` 사용은 지양해야 한다.

> DB에 단순한 정수값이 저장되게 되고 차후 enum의 값들이 변경될 시 이에 충돌된다.
>
> `STRING` 옵션을 사용하면 문자열 자체가 저장되 이를 방지할 수 있다



## 기본 키 매핑

기본 키를 지정. @Id, @GeneratedValue

* @id: 개발자가 직접 설정
* @GeneratedValue: 자동으로 생성



### @GeneratedValue

| 옵션     | 설명                                                       |
| -------- | ---------------------------------------------------------- |
| IDENTITY | 데이터베이스에 위임                                        |
| SEQUENCE | 데이터베이스 시퀀스 오브젝트 사용. @SequenceGenerator 필요 |
| TABLE    | 키 생성용 테이블을 사용. @TableGenerator 필요              |
| AUTO     | 방언에 따라 자동 지정, 기본값                              |



### 권장하는 전략

기본 키는 null이면 안되고, 유일해야 하며 `변하면 안된다`.

언제나 위의 속성을 만족하기 위해서는 자연키 대신 대체키를 사용하는 것이 유리하다.(ex. uuid)



#  연관관계 매핑

관계형 DB에서는 Foreign key를 통한 양뱡향 join이 가능하다. 하지만 연관관계가 없이 객체를 관리한다면 참조가 아닌 관계형 DB처럼 식별자값을 가지고 객체를 식별해야 한다. -> 객체 지향적인 방법이 아니다

이러한 객체지향과 관계지향적인 요소의 차이를 맞추기 위해 연관관계 매핑을 수행한다



## 단방향 연관관계

![단방향](https://user-images.githubusercontent.com/32065940/123116986-54487500-d47c-11eb-98a8-33461d706c0b.png)

``` java
@Entity
public class Member{
    @Id @GeneratedValue
    private Long id;
    
    @Column(name = "USERNAME")
    private String name;
    private int age;
    
    @ManyToOne
    @JoinColumn(name = "TEAM_ID")
    private Team team;
}
```

위의 uml과 코드 처럼 객체안에 식별자 값을 갖는 대신 실제 객체를 갖게 된다.



### 어노테이션

* @ManyToOne, @OneToMany, @OneToOne, @ManyToMany

  앞에 오는 것이 기준이라고 생각

  위의 코드에서 ManyToOne을 쓴 이유는 Mebmer가 N 대 1 관계를 이루기 때문

* @JoinColumn

  join에서 사용될 Column을 식별



## 양방향 연관관계

![양방향](https://user-images.githubusercontent.com/32065940/123117523-c620be80-d47c-11eb-9801-399c4b8c824d.png)

``` java
@Entity
public class Team {
    
    @Id @GeneratedValue
    private Long id;
    
    private String name;
    
    @OneToMany(mappedBy = "team")
    List<Member> members = new ArrayList<Member>();
}
```

위의 uml과 코드처럼 Member 객체 뿐만 아니라 Team 객체에서도 참조를 유지한다.



### mappedBy

> 양방향은 사실 쌍방으로 2개의 단방향 연관관계를 맺는 것이다

![mappedby](https://user-images.githubusercontent.com/32065940/123118485-a50c9d80-d47d-11eb-8ec5-5703b4762c00.png)

위의 uml을 살펴보면 양방향 연관관계는 **DB 테이블에 영향을 끼치지 않는다**는 것을 알 수 있다.

> Foreign Key인 *TEAM_ID*는 *Member* 객체에서 *team*이 변경될 때와 *Team* 객체에서 *members*가 변경될 때 언제 관리가 되어야 하는가?

 위의 사항을 처리하기 위해서 `연관관계의 주인`을 설정한다

* 둘 중 하나를 연관관계의 주인으로 지정
* 지정된 변수만이 외래 키를 관리(등록, 수정)
* 주인이 아닌 경우 읽기만 가능
* 주인이 아닌 변수가 mappedBy 어노테이션을 사용하여 주인을 지정



### 어떤 것을 주인으로 지정해야 하는가?

외래 키가 있는 곳을 주인으로 정하자

> 만약 외래 키가 없는 곳을 주인으로 정할 경우 Team 값을 변경하는 데에 있어 다른 테이블도 변경되는 경우가 있어 복잡해지고 성능 이슈가 있을 수 있다



### 주의

1. 양방향 연관관계시 주인이 아닌 것을 변경시 FK가 null로 들어가게 된다

2. JPA가 없을 경우도 고려하여 항상 양쪽 모두 값을 설정하는 것이 권장

   1. 이를 보장하기 위해서 추가하는 함수안에 반대방향으로 추가하는 함수를 수행하는 연관관계 편의 메소드 사용 권장

3. 무한 루프가 생성되는 경우를 조심 (ex. toString())

   > 서로 참조하기에 고려햐여 함수를 사용해야 한다

4. 단방향 매핑만으로 이미 연관관계 매핑은 완료

   1. 역방향으로 탐색할 일이 없다면 양방향은 필요가 없다
   2. 양방향 연관관계는 기존 코드에 추가가 쉽다 -> 테이블에 영향을 안주기 때문에
   3. 추후에 필요시점에 추가하는 것이 권장
