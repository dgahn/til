# 프록시와 연관관계 정리

## 1.프록시를 사용하는 이유

**Member**를 조회할 때 **Team**도 조회해야할까? **Member**의 정보만 필요한 경우 **Team**에 대한 정보를 가져오는 것은 낭비가 될 수 있다. 그래서 **JPA**에서 제공해주는 기능이 **프록시**와 **지연로딩**이다.

## 2. 가짜(프록시) 객체 조회

**JPA**에서 제공하는 메소드 중에 **em.find()** 말고 **em.getReference()** 라는 메소드가 있다. 이는 실제로 데이터베이스에서 값을 조회하는 것이 아니라 가짜 객체를 생성해서 반환해준다.

```java
Member findMember = em.getReference(Member.class, member.getId());
```

이 때, **em.getReference(Member.class, member.getId())** 를 호출하는 시점에는 데이터베이스에서 값을 호출하지 않는다. 하지만

```java
Member findMember = em.getReference(Member.class, member.getId());
findMember.getId();
```

**em.getId()** 를 하는 순간 실제로 데이터베이스로부터 값을 가져온다.

#### 프록시 객체란?

프록시 객체는 실제 **Entity**를 상속해서 **JPA**가 만든 하위 객체이다. 모양만 따라 만든 것이긴데 사용하는 입장에서는 진짜 객체인지 프록시 객체인지 구분하지 않고 사용하면 된다. 

프록시 객체를 통해 값을 조회하는 절차는 아래와 같다.

1. em.getReference를 통해서 프록시 객체를 가져온다.
2. 프록시 객체로부터 **get** 메소드를 통해 값을 조회한다.
3. 프록시 객체가 영속성 컨텍스트에 값이 있는지 조회한다.
4. 영속성 컨텍스트는 값이 없으면 데이터베이스로부터 값일 조회하여 실제 엔티티를 생성한다.
5. 프록시 객체에 있는 **target**이라는 변수를 실제 엔티티와 연결해준다.
6. **target**의 **get** 메소드를 통해 진짜 값이 조회된다.

프록시 객체는 처음 사용할 때 한번만 초기화 되고 프록시 객체는 실제 엔티티로 변경되는 것이 아니라 엔티티를 참고하는 것 뿐이다. 프록시 객체는 원본 엔티티를 상속 받고 따라서 타입 체크시 주의해야한다. 타입 비교는 **instance of**로 해야한다.

```java
Member member1 = em.getReference(Member.class, 1L);
Member member2 = em.getfind(Member.class, 2L);

System.out.println(member1 istanceof Member); // == 비교를 하면 안된다.
System.out.println(member2 istanceof Member);
```

영속성 컨텍스트에 이미 엔티티가 존재하는 경우 **em.getReference()** 를 호출해도 실제 엔티티를 호출한다. 그 이유는 JPA에서는 하나의 영속성 컨텍스트에 같은 값을 조회하면 항상 같아야한다. 그렇기 때문에 **em.reference()** 를 사용해도 실제로 같은 엔티티가 나온다. 하지만 반대로 **em.getReference()** 를 조회하고 **em.find()**를 하면 실제 엔티티가 반환되는게 아니라 프록시가 반환된다.

영속성 컨텍스트를 (em.flush(), em.close())등을 통해서 초기화한 다음에 프록시 객체를 통해서 엔티티 값을 가져오려고 하면 에러가 발생한다.

#### 프록시 객체 확인

- 프록시 인스턴스의 초기화 여부 확인 : emf.getPersistenceUnitUtil().isLoaded(프록시)
- 프록시 클래스 확인 방법 : 프록시.getClass()
- 프록시 강제 초기화 : Hibernate.initialize(프록시)
  - JPA 표준에는 강제 초기화가 없다. 이 기능은 하이버네이트에서 제공해주는 기능이다.

## 3. 즉시 로딩과 지연로딩


```java
@Entity
public class Member {
    ... 생략
    @ManyToMany(fetch = FetchType.LAZY)
    @JoinColumn(name = "TEAM_ID")
    private Team team;
    ... 생략
}
```

위와 같이 **@ManyToMany(fetch = FetchType.LAZY)** 으로 세팅하면 연관된 객체에서 대해서 프록시객체로 가져온다. 이를 **LAZY LOADING(지연 로딩)** 이라고 부른다.

```java
@Entity
public class Member {
    ... 생략
    @ManyToMany(fetch = FetchType.EAGER)
    @JoinColumn(name = "TEAM_ID")
    private Team team;
    ... 생략
}
```

위와 같이  **@ManyToMany(fetch = FetchType.EAGER)** 으로 세팅하면 연관된 객체에 대해서 바로 조인해서 엔티티로 가져온다.

#### 프록시와 즉시로딩 주의

가급적이면 지연 로딩만 사용하는 것이 좋다. 즉시 로딩을 적용하면 예상하지 못한 SQL 발생된다. 예를 들어, JPQL를 작성하는 경우 즉시로딩을 하면 참조하는 테이블을 조인이 아닌 개별적인 **SELECT**문으로 조회한다. 아래의 코드를 보자

```java
em.createQuery("select m from Member m", Member.class);
```

위는 **Member**를 조회할 때, **Team**을 같이 조회한다. 만약 이런 참조가 여러개라고 하면 불필요한 테이블을 모두 조회함으로 **지연 로딩**을 사용하는 것이 좋다.


**@ManyToOne**, **@OneToOne**은 기본이 즉시로딩이기 때문에 지연로딩을 설정해줘야한다. **@OneToMany**, **@ManyToMany**는 기본이 지연로딩이다.

## 4. 영속성 전이(CASCADE)와 고아 객체

특정 엔티티를 영속 상태로 만들 때 연관된 엔티티도 함께 영속하려는 경우를 말한다. 

```java
@Entity
public class Parent {

    @Id
    @GeneratedValue
    private Long Id;

    @OneToMany(mappedBy = "parent")
    private List<Child> childList = new ArrayList<>();

    public void addChild(Child child) {
        childList.add(child);
        child.setParent(this);
    }

}
```

```java
@Entity
public class Child {

    @Id
    @GeneratedValue
    private Long Id;

    @ManyToOne
    @JoinColumn(name = "parent_id")
    private Parent parent;

}
```

```java
Child child1 = new Child();
Child child2 = new Child();

Parent parent = new Parent();
parent.addChild(child1);
parent.addChild(child2);

em.persist(parent);
em.persist(child1);
em.persist(child2);
```

이렇게 작성하면 **parent**를 영속성 컨텍스트에 추가 했을 때, 자동으로 **child**들이 영속성 컨텍스트에 추가 됬으면 하는 면이 있다. 그래서, 사용하는 옵션이 **cascade** 이다. 사용법은 아래와 같다.

```java
@Entity
public class Parent {

    @Id
    @GeneratedValue
    private Long Id;

    @OneToMany(mappedBy = "parent", cascade = "CascadeType.ALL")
    private List<Child> childList = new ArrayList<>();

    public void addChild(Child child) {
        childList.add(child);
        child.setParent(this);
    }

}
```

그러면 메인 메소드를 아래와 같이 작성할 수 있다.

```java
Child child1 = new Child();
Child child2 = new Child();

Parent parent = new Parent();
parent.addChild(child1);
parent.addChild(child2);

em.persist(parent);
```

주로 하나의 자식이 하나의 부모에만 해당할 때 사용한다.

## 5. 고아객체

부모 엔티티와 연관관계가 끊어진 자식 엔티티를 자동으로 삭제하는 기능이다.

```java
@Entity
public class Parent {

    @Id
    @GeneratedValue
    private Long Id;

    @OneToMany(mappedBy = "parent", cascade = "CascadeType.ALL", orphanRemoval = true)
    private List<Child> childList = new ArrayList<>();

    public void addChild(Child child) {
        childList.add(child);
        child.setParent(this);
    }

}
```

```java
@Entity
public class Child {

    @Id
    @GeneratedValue
    private Long Id;

    @ManyToOne
    @JoinColumn(name = "parent_id")
    private Parent parent;

}
```

```java
Child child1 = new Child();
Child child2 = new Child();

Parent parent = new Parent();
parent.addChild(child1);
parent.addChild(child2);

em.persist(parent);

em.flush();
em.clear();

Parent findParent = em.find(Parent.class, parent.getId());
findParent.getChildList().remove(0);
```

위와 같이 영속성 컨텍스트를 비우고 **Parent**를 가져오면 **Parent**에 해당하는 **Child**까지 모두 조회할 것이다. 그런데 **Parent**에서 **Child**를 삭제한다면 둘의 연관관계만 삭제해야하는데 **orphanRemoval = true** 속성을 통해서 영속성 컨텍스트에서 **Child**까지 삭제된다.

#### 고아 객체의 사용 주의

참조가 제거된 엔티티는 다른 곳에서 참조하지 않는 고아 객체로 보고 삭제하는 기능이다. 그렇기 때문에 참조하는 곳이 하나일 때만 사용해야한다. 즉, 특정 엔티티가 개인 소유할 때 사용가능하다는 의미다. 

**@OneToOne**, **@OneToMany**만 사용 가능하다. 부모를 제거할 때 자식도 제거되는데 마치 **CasCadeType.REMOVE**처럼 동작한다.

#### 영속성 전이 + 고아 객체, 생명주기

스스로 생명주기를 관리하는 엔티티느 **em.persist()**로 영속화하고 **em.remove()**로 제거한다. 그에 반해 **CasCadeType.ALL + orphanRemoval=true**를 모두 활성화하면 부모 엔티티를 통해서 자식의 생명주기를 관리할 수 있다.

이는 도메인 주도 설계(DDD)의 Aggregate Root 개념을 구현할 때 유용하다.