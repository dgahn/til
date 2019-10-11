# 고급 매핑

## 1. 상속관계 매핑

관계형 데이터베이스에서는 상속관계가 없다. 대신 슈퍼타입 서브타입 관계라는 모델링 기법으로 객체의 상속과 유사하게 사용할 수 있다.

객체의 상속관계를 데이터베이스에서 구현하는 방법은 아래 3가지가 있다.

- 조인 전략

- 단일 테이블 전략

- 구현 클래스마다 테이블 전략

#### 조인 전략

```java
@Entity
@Inheritance(strategy = Inheritance.JOINED)
public class Item {

    @Id @GeneratedValue
    private Long id;
    
    private String name;
    private int price;
}
```

```java
@Entity
public class Album extends Item{

    private String artist;

}
```

```java
@Entity
public class Movie extends Item {

    private String director;

    private String actor;

}
```

```java
@Entity
public class Book extends Item {

    private String author;

    private String isbn;

}
```

**@Inheritance(strategy = Inheritance.JOINED)** 을 사용하면 JPA에게 상속관계의 객체 모델을 조인 전략을 사용할 것이라고 알려준다. 그래서 모두 개별의 테이블이 생기고 **ID**를 **ITEM**의 **ID**를 사용하게 된다.

```java
@Entity
@Inheritance(strategy = Inheritance.JOINED)
@DisciminatorColumn(nmae = "DTYPE")
public class Item {

    @Id @GeneratedValue
    private Long id;
    
    private String name;
    private int price;
}
```

위의 **@DisciminatorColumn(nmae = "DTYPE")** 를 추가하면 **DTYPE**이라는 것이 추가되면서 **ITEM** 테이블에 **Album, Movie, Book** 중 무슨 타입인지에 대한 값이 들어간다.

```java
@Entity
@DisciminatorValue("book")
public class Book extends Item {

    private String author;

    private String isbn;

}
```
**@DisciminatorValue("book")** 애노테이션을 통해서 테이블의 이름도 변경할 수 있다.

#### 단일 테이블 전략

상속구조의 객체를 하나의 테이블로 만들 수 있다.

```java
@Entity
@Inheritance(strategy = Inheritance.SINGLE_TABLE)
@DisciminatorColumn(nmae = "DTYPE")
public class Item {

    @Id @GeneratedValue
    private Long id;
    
    private String name;
    private int price;
}
```

**@Inheritance(strategy = Inheritance.SINGLE_TABLE)** 만 변경하면 된다. 주의할 점은 **@DisciminatorColumn(nmae = "DTYPE")** 을 명시하지 않으면 어떤 하위 클래스인지 테이블에서 구분하기 어렵기 때문에 반드시 넣어주는게 좋다.

#### 구현 클래스마다 테이블 전략

```java
@Entity
@Inheritance(strategy = Inheritance.TABLE_PER_CLASS)
public abstract class Item {

    @Id @GeneratedValue
    private Long id;
    
    private String name;
    private int price;
}
```

**@Inheritance(strategy = Inheritance.TABLE_PER_CLASS)** 을 사용하고 **ITEM** 클래스를 추상 클래스로 만들면 **ITEM**에 대한 테이블은 생성되지 않고 **Album, Movie, Book**에 대한 테이블만 생긴다.

단순해서 좋지만 아래의 경우에 문제가 발생한다.

```java
Item item = em.find(Item.class, movie.getId());
```

위와 같이 조회를 하면 어디에 ID가 존재하는지 모르기 때문에 **Album, Movie, Book** 모두 조회해야한다.

#### 각 전략의 장단점

#### 조인 전략 
- 장점 
  - 데이터가 정규화 되어 있다.
  - 제약조건을 **ITEM**에만 걸면 다른 테이블에 모두 적용이 된다.
  - 외래 키 참조 무결성 제약조건을 활용할 수 있다.
  - 저장공간을 효율적으로 사용한다.
- 단점
  - 조회할 때, 조인을 많이 사용해야하기 때문이나 성능이 낮고 조회 쿼리가 복잡한다.
  - 데이터 저장시 INSERT SQL이 2번 호출된다.

기본적으로 조인 전략을 선택하는 것이 좋다.

#### 단일 테이블 전략

- 장점
  - 조인이 없으면 조회 성능이 좋다.
  - 조회 쿼리가 단순하다.
- 단점
  - 자식 엔티티가 매핑한 컬럼은 모두 null을 허용한다.
  - 단일 테이블에 모든 것을 저장하므로 테이블이 커질 수 있는 상황에 따라서 조회 성능이 오히려 느려질 수 있다.

#### 구현 클래스마다 테이블 전략

쓰지 않는 것이 좋다. 단점만 말하면 유연성이 부족하고 조회할 때, UNION을 사용하기 때문에 성능이 떨어진다.

<br>

## @MappedSuperclass

공통 매핑 정보가 필요할 때 사용하는 애노테이션이다. 테이블에서 공통적으로 사용하는 속성들이 있을 경우 아래와 같이 사용할 수 있다.

```java
@MappedSuperclass
public class BaseEntity {

  private String createdBy;
  private LocalDateTime createdDate;
  private String lastModifiedBy;
  private LocalDateTime lastModifiedDate;

}
```

```java
@Entity
public class Member extends BaseEntity {
.. 생략
}
```


```java
@Entity
public class Team extends BaseEntity {
.. 생략
}
```

위와 같이 작성하면 **BaseEntity**라는 테이블은 생성되지 않지만 **Member**와 **Team**에 **BaseEntity**의 속성을 부여해서 사용할 수 있다.

주의할 점은 상속관계 매핑이 아니고 엔티티도 아니다. 그리고 테이블과 매핑되지도 안흔ㄴ다. 하위클래스에 매핑 정보만 제공하고 엔티티가 아니기 때문에 상위 타입으로 조회할 수 없다.

```java
em.find(BaseEntity.class, 1L); // 불가능
```

직접 생생해서 사용할일 없으므로 추상 클래스로 선언하는 것을 추천한다. 그리고 **JPA**에서는 **Enity**와 **MappedSuperclass**가 없으면 상속을 사용할 수 없다.