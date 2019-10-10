# 연관관계 매핑

## 1. 연관관계 매핑 기초

객체와 테이블 연관관계의 차이를 이해해야한다. 객체는 다른 객체를 참조하고 테이블은 그 값을 외래키를 통해서 매핑되어 있다. 

아래의 용어들에 대해서 숙지하자.

- 방향(Direction) : 단방향, 양방향
- 다중성(Multiplicity) : 다대일(N:1), 일대다(1:N), 일대일(1:1), 다대다(N:M) 이해
- 연관관계의 주인(Owner) : 객체 양방향 연관관계는 관리 주인이 필요하다.

<br>

#### 데이터베이스 관점으로 설계하는 경우

아래와 같이 엔티티를 작성해보자.

```java
@Entity
public class Member {

    @Id @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;

    @Column(name = "USERNAME")
    private String username;

    @Column(name = "TEAM_ID")
    private Long teamId;

}
```

```java
@Entity
public class Team {
    @Id @GeneratedValue
    @Column(name = "TEAM_ID")
    private Long id;

    @Column(name = "TEAMNAME")
    private String name;
}
```

```java
Team team = new Team();
team.setName("TeamA");
em.persist(team);

Member member = new Member();
member.setUsername("member1");
member.setTeamId(team.getId); // 객체 지향적이지기가 않다. member.setTeam()과 같이 진행해야한다.
em.perist(member);
```

전에 말했듯이 객체 그래프를 이용해서 객체를 그대로 가져오고 세팅할 수 있어야한다. 마치 **컬렉션**을 사용하는 것처럼 만들어야하는데 위는 그렇지 못하다.

**Member**의 **Team**을 구하는 아래의 코드를 보자.

```java
Member findMember = em.find(Member.class, member.getId());
Team findTeam = em.find(Team.class, team.getId()); 
```

바로 **Team**을 못가져오기 때문에 번잡하게 가져오는 문제가 있다.

이는 객체를 테이블에 맞춰 데이터 중심으로 모델링하면, 협력 관계를 만들 수 없기 때문이다. 테이블은 외래 키로 조인을 사용해서 연관된 테이블을 찾는데 객체는 참토를 사용해서 연관된 객체를 찾기 때문에 서로 다른 패러다임에 의해서 생기는 문제이다.

<br>

#### 객체 지향 관점으로 설계하는 경우

```java
@Entity
public class Member {

    @Id @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;

    @Column(name = "USERNAME")
    private String username;

    @Column(name = "TEAM_ID")
    private Team team;

}
```

```java
@Entity
public class Team {
    @Id @GeneratedValue
    @Column(name = "TEAM_ID")
    private Long id;

    @Column(name = "TEAMNAME")
    private String name;
}
```

이렇게만 작성하면 **JPA**는 위의 관계를 해석할 수가 없다. 그래서 **Member** 클래스에 **@ManyToOne**과 **@JoinColumn** 애노테이션을 추가해야한다.

```java
@Entity
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

**Member** 클래스 입장에서 **Team** 클래스와의 관계는 **N:1**이다. 그렇기 때문에 **@ManyToOne** 애노테이션을 추가하고 **외래키**를 알려주기 위해서 **@JoinColumn(name = "TEAM_ID")**을 추가한다.

그러면 아까 작성한 코드가 아래처럼 변경된다.

```java
Team team = new Team();
team.setName("TeamA");
em.persist(team);

Member member = new Member();
member.setUsername("member1");
member.setTeam(team);
em.perist(member);

Team findTeam = member.getTeam();
```

## 2. 양방향 연관관계와 연관관계의 주인

객치 지향에서 양방향 관계는 사실 단방향 두 개를 합친 것이다. 아래의 코드를 보면서 단방향과 어떻게 다른지 살펴보도록 하자.

```java
@Entity
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
@Entity
public class Team {
    @Id @GeneratedValue
    @Column(name = "TEAM_ID")
    private Long id;

    @Column(name = "TEAMNAME")
    private String name;
}
```

위의 코드를 보면 **Member** 클래스에서는 **Team**을 찾을 수 있지만 **Team** 클래스에서는 **Member**를 찾을 수 없다. 데이터베이스 같은 경우 **외래키**를 이용하여서 손쉽게 양쪽을 찾을 수 있다. **JPA**는 객체 그래프를 제공해주기 위해서 아래와 같은 문법을 제공해준다.

```java
@Entity
public class Team {
    @Id @GeneratedValue
    @Column(name = "TEAM_ID")
    private Long id;

    @Column(name = "TEAMNAME")
    private String name;

    @OneToMany(mappedBy = "team") // 매핑 되는 곳에서 클래스의 변수명이 무엇인지 작성한다.
    private List<Member> members = new ArrayList<Member>(); // NullPointException을 예방하기 위해 관례적으로 리스트를 생성한다.

}
```

- **mappedBy** : 객체의 두 관계 중에서 연관관계의 주인을 지정해야하는데 연관관계의 주인만이 외래 키를 관리한다. 그리고 주인이 아닌 쪽은 읽기만 가능하다. 주인은 mappedBy를 쓰지 않는데 위와 같은 경우 **Member**가 주인이 된다.

외래키를 있는 곳을 주인으로 정하는 것을 추천한다. 그 이유는 여러가지가 있지만 가장 중요한 것은 기본키를 사용하는 곳으로 사용했다가 자기가 원하는 튜플이 아니라 다른 튜플을 수정할 수도 있기 때문이다. 위 예제에서는 팀의 다른 멤버를 바꿀 수도 있다는 이야기이다.

## 양방향 매핑시 주의할 점

양방향 매핑시 연관관계의 주인에 값을 입력해야한다. 하지만, 순수한 객체 관계를 고려하면 항상 양쪽 다 값을 입력해야 한다.

```java
Team team new Team();
team.setName("TeamA");
em.persist(team);

Member member = new Member();
member.setUsername("member1");
member.setTeam(team);
em.persist(member);

team.getMembers().add(member);
```

만약 위와 같이 하지 않고 **team.getMembers().add(member)**를 삭제하고  아래와 같이 작성했다면

```java
Team team new Team();
team.setName("TeamA");
em.persist(team);

Member member = new Member();
member.setUsername("member1");
member.setTeam(team);
em.persist(member);
```

 **team.getMembers()** 했을 때, 아무런 값이 안 나온다. 그 이유는 **지연 로딩**에 의해서 **team.getMembers()**의 값은 호출 하는 시점에 **데이터베이스**로부터 값을 가져온다. 하지만 아직 실제 데이터베이스로 **flush**한 것이 아니기 때문에 값이 반영이 안되있어서 조회했을 때, **team.getMembers()**는 빈 껍데기만 남을 것이다.

또 테스트 케이스를 작성할 때, 값이 **empty**로 나오는 경우가 있다. 그러므로 양방향 매핑을 했을 때는 양쪽으로 값을 넣어주는 것이 좋다.

그래서 연관관계 매핑을 할 때는 아래와 같이 코드를 작성해주는 것이 좋다.


```java
public class Member {
... 생략
    public void setTeam(Team team) {
        this.team = team;
        team.getMembers().add(this);
    }
... 생략
}
```

위와 같이 작성하면 자동으로 양방향으로 추가할 수 있다. 연관관계를 세팅하는 메소드는 메소드명을 아래와 같이 하는 것을 추천한다. Java의 Setter와 구분하기 위함이다.

```java
public class Member {
... 생략
    public void changeTeam(Team team) {
        this.team = team;
        team.getMembers().add(this);
    }
... 생략
}
```

또는 아래의 코드로 작성한다.

```java
public class Team {
... 생략
    public void addMember(Team team) {
        member.setTeam(team);
        members.add(member);
    }
... 생략
}
```

양방향 매핑시 위와 같이 양쪽에서 코드를 작성하면 **무한 루프**를 조심해야한다. 무한루프가 생성되는 경우는 대체적으로 아래와 같다.

- toString()
- lombok
- JSON 생성 라이브러리

그러므로 **lombok**에서 **toString()** 은 빼고 사용하는 것이 좋고 컨트롤러에서는 **엔티티**를 절대 반환하면 안된다.

<br>

## 양방향 매핑 정리

단방향 매핑만으로도 이미 연관관계 매핑은 완료된 것이다. 양방향 매핑은 반대 방향으로 조회(객체 그래프 탐색) 기능이 추가된 것 뿐이다. **JPQL**에서 역방향으로 탐색할 일이 많다. 그러므로 단방향 매핑을 잘하고 필요에 따라 양방향을 추가해도 된다. 왜냐하면 테이블에 영향을 주지 않기 때문이다.