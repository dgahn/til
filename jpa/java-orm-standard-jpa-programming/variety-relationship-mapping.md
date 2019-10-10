# 다양한 연관관계 매핑

## 1. 연관관계 매핑시 고려사항 3가지

#### 다중성(다대일, 일대다, 일대일, 다대다)

JPA에서 제공하는 아래의 애노테이션을 통해서 다중성을 정의할 수 있다.

- 다댸일 : @ManyToOne

- 일대다 : @OneToMany

- 일대일 : @OneToOne

- 다대다 : @ManyToMany

#### 단방향, 양방향

테이블은 외래 키 하나로 양쪽 조인이 가능하기 때문에 사실 방향이라는 개념이 없다. 객체는 참조용 필드가 있는 쪽으로만 참조 가능하다. 그렇기 때문에 한쪽만 참조하면 단방향이고 양쪽이 서로 참조하면 양방향이 된다.
참고로 양방향이라는 것은 사실상 단방향이 2개가 합쳐진 것으로 양방향이라는 개념이 없다. 굳이 양방향이 2개가 아니라고 하는 이유는 연관관계의 주인을 명확히 이해하기 위함이다.

#### 연관관계의 주인

객체는 양방향 관계는 A->B, B->A 처럼 참조가 2군데이다. 객체는 양방향 관계는 참조가 2군데 있다보니 둘 중 테이블의 외래 키를 관리할 곳을 지정해야한다. 그래서 아래와 같이 정해서 사용한다.

- 연관관계의 주인 : 외래 키를 관리하는 참조
- 주인의 반대편 : 외래 키에 영향을 주지 않음, 단순히 조회만 함

<br>

## 2. 다대일[N:1]

팀과 멤버의 관계는 **다대일**이다. 코드로 표현하면 아래와 같다.

```java
public class Member {
    @Id @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;

    @Column(name = "USERNAME")
    private String username;

    @ManyToOne
    @JoinColumn(name = "TEAM_ID")
    private Team team;
}
```

```java
public class Team {
    @Id @GeneratedValue
    @Column(name = "TEAM_ID")
    private Long id;

    private String name;
}
```

<br>

위는 단방향일 때, 관계를 코드로 보여준 것이고 양방향인 경우 **Team** 클래스를 아래와 같이 변경하면 된다.

```java
public class Team {
    @Id @GeneratedValue
    @Column(name = "TEAM_ID")
    private Long id;

    @OneToMany(mappedBy = "team")
    private List<Member> members = new ArrayList<>();

    private String name;
}
```

주의할 점은 **Team** 클래스에서 **members**는 읽기만 가능하다.

<br>

## 3. 일대다[1:N]

일대다 단방향 매핑일 때는 코드가 아래와 같이 작성된다.

```java
public class Member {
    @Id @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;

    @Column(name = "USERNAME")
    private String username;

}
```

```java
public class Team {
    @Id @GeneratedValue
    @Column(name = "TEAM_ID")
    private Long id;

    @OneToMany
    @JoinColumn(name = "TEAM_ID")
    private List<Member> members = new ArrayList<>();

    private String name;
}
```

```java
Member member = new Member();
member.setUsername("member1");

em.perist(member);

Team team = new Team();
team.setName("teamA");
team.getMembers.add(member);

em.persist(team)
```

위와 같이 매핑을 하고 아래와 같이 코드를 실행시키면 **Member**와 **Team**이 **INSERT**되고 **Member**의 **TEAM_ID**가 업데이트 된다.

약간의 낭비가 있지만 이것보다 심각한 문제는 **Team**을 업데이트 했을 때, **Member**의 속성이 변경되기 때문에 다른사람이 잘 모를 수도 있다는 것이다.

#### 일대다 단방향 정리

일대다 단방향은 일대다에서 일이 연관관계의 주인이다. 테이블에서 일대다 관계는 항상 다쪽에 외래 키가 있다. 그런데 이 구조에서는 테이블의 외래 키를 반대편에서 관리하는 특이한 구조이다. 반드시, @JoinColumn을 사용해야한다. 그렇지 않으면 조인 테이블 방식(중간 테이블)을 사용한다.

일대다 단방향의 단점은 엔티티가 관리하는 외래 키가 다른 테이블에 있고 연관관계 관리르 위해 추가로 **UPDATE SQL**을 실행하게 된다. 그렇기 때문에 일대다 단방향 매핑보다는 **다대일 양방향 매핑**을 사용하는 것이 좋다.


#### 참고
일대다 양방향도 있지만 사용하지 말자.

<br>

## 4. 일대일[1:1]

일대일은 주 테이블이나 대상 테이블 중에 외래 키를 선택할 수 있다. 단, 외래 키에 데이터베이스 유니크(UNI) 제약조건이 추가되야한다.

**Member** 클래스가 단 하나의 **Locker** 클래스를 가지고 있다고 가정하자. 코드를 작성하면 아래와 같다.

```java
public class Locker {

    @Id @GeneratedValue
    private Long id;

    private String name;

}
```

```java
public class Member {
     @Id @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;

    @Column(name = "USERNAME")
    private String username;

    @OneToOne
    @JoinColumn(name = "LOCKER_ID")
    private Locker locker;
}
```

만약, 양방향으로 묶고 싶다면 아래와 같이 코드를 변경하면 된다.

```java
public class Locker {

    @Id @GeneratedValue
    private Long id;

    @OneToOne
    @JoinColumn(mappedBy = "locker")
    private Member member;

}
```

일대일 관계에서 대상 테이블에 외래 키 단방향은 JPA에서 지원하지 않는다. 그러나 양방향 관계는 지원하기 때문에 필요하면 위와 같이 사용하면 된다.

<br>

## 5. 다대다[N:M]

#### 실무에서는 사용하지 않는 것이 좋다.

테이블간에는 다대다가 설립이 안되기 때문에 중간에 연결 테이블을 생성해서 사용한다. 하지만 객체에서는 객체 2개가 다대다 관계가 가능하다. 그래서 JPA에는 아래와 같이 사용하면 테이블을 자동으로 생성해준다.

```java
public class Product {

  @Id @GeneratedValue
  private Long id;

  private String name;

}
```

```java
public class Member {
... 생략
  @ManyToMany
  private List<Product> products = new ArrayList<>();
... 생략
}
```

그 이유는 테이블이 단순히 연결만 하는 테이블이 아닐 가능성이 크기 때문이다. 연결 테이블에 다른 속성들이 들어가는데 이렇게 사용하면 다른 속성들을 넣을 수가 없다.

#### 다대다 한계 극복

연결 테이블용 엔티티를 별도로 생성한다.

```java
@Entity
puiblic class MemberProduct {

  @Id @GeneratedValue
  private Long id;

  @ManyToOne
  @JoinColumn(name = "MEMBER_ID")
  private Member member;

  @ManyToOne(name = "PRODUCT_ID")
  private Product product;

  private int count;
  private int price;

  private LocalDateTime orderDateTime;

}
```

```java
@Entity
puiblic class Member {
... 생략
  @OneToMany(mappedBy = "member")
  private List<MemberProduct> memberProducts = new ArrayList<>();
... 생략
}
```


```java
@Entity
puiblic class Product {
... 생략
  @OneToMany(mappedBy = "product")
  private List<MemberProduct> memberProducts = new ArrayList<>();
... 생략
}
```