# 값 타입

## 1. 개요

JPA는 데이터 타입을 **엔티티 타입**과 **값 타입**으로 분류한다.

#### 엔티티 타입

**@Entity**로 정의하는 객체로 데이터가 변해도 식별자로 지속해서 추적이 가능하다. 예를 들어, 회원 엔티티의 키나 나이 값을 변경해도 식별자로 인식이 가능하다.

#### 값 타입

**int**, **Integer**, **String**처럼 단순히 값으로 사용하는 자바 기본 타입이나 객체를 의미한다. 식별자가 업속 값만 있으므로 변경시 추적이 불가능하다. 예를 들어, 숫자 100을 200으로 변경하면 완전히 다른 값으로 대체 가능하다.

#### 값 타입의 분류

- 기본값 타입
  - 자바 기본 타입(int, double)
  - 래퍼 클래스(Integer, Long)
  - String
- 임베디드 타입(embedded type, 복합 값 타입)
- 컬렉션 값 타입(collection value type)

<br>

## 2. 기본값 타입

기본 값 타입은 생명주기를 엔티티에 의존한다. 예를 들어서 회원을 삭제하면 이름, 나이 필드도 함께 삭제된다. 그리고 다른 객체와 값 자체를 공유하면 안된다.

<br>

## 3. 임베디드 타입(복합 값 타입)

JPA에서는 새로운 값 타입을 직접 정의할 수 있다. 이를 **임베디드 타입**이라고 부른다. 주로 기본 값 타입을 모아 만들어 복합 값 타입이라고 부른다. **int**, **String**과 같은 값 타입이다.

예를 들어 아래의 테이블을 보자.

|Membmer|
|:---|
|id|
|name|
|startDate|
|endDate|
|city|
|street|
|zipcode|

시작일과 종료일 또는 주소들은 자바 애플리케이션은 묶어서 사용할 수 있다. 그래서 아래와 같이 표현될 것이다.

|Membmer|
|:---|
|id|
|name|
|workPeriod|
|homeAddress|

그러면 **wordPeriod**와 **homeAddress**는 별도의 클래스로 분리해서 사용할 수 있다. 그 때 JPA에서 어떻게 사용하는지 한번 살펴보자.

```java
@Entity
public class Member {
    @Id
    @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;

    @Column(name = "USERNAME")
    private String username;

    @Embedded
    private Period period

    @Embedded
    private Address homeAddress;
}
```

```java
@Embeddable
public class Period {
    private LocalDateTime startDate;
    private LocalDateTime endDate;

}
```
```java
@Embeddable
public class Address {
    private String city;
    private String street;
    private String zipCode;
}
```

```java
Member member = new Member();
member.setName("hello");
member.setHomeAddress(new Address("city", "street", "zipCode"))
member.setWorkPeriod(new Period());

em.persist(member);
```

**@Embeddable**과 **@Embedded** 둘 중 하나를 사용해도 임베디드 타입을 사용할 수 있다. 하지만 둘 다 쓰는 것을 권장한다. 주의할 점은 임베디드 타입의 클래스에는 기본 생성자가 만드시 있어야한다.

#### 임베디드 타입의 장점

재사용이 가능하고 응집도가 높다. 그래서 Period.isWork()처럼 해당 값 타입만 사용하는 의미있는 메소드를 만들 수 있다. 임베디드 타입을 포함한 모든 값 타입은, 값 타입을 소유한 엔티티에 생명주기를 맡긴다.

#### 임베디드 타입과 테이블 매핑

임베디드 타입은 엔티티의 값일 뿐이고 임베디드 타입을 사용하기 전과 후에 매핑하는 테이블은 같다. 객체와 테이블을 아주 세밀하게 매핑하는 것이 가능하고 잘 설계한 ORM 애플리케이션은 매핑한 테이블의 수보다 클래스의 수가 더 많다.

#### @AttributeOverride: 속성 재정의

한 엔티티에서 같은 값 타입을 사용하면 컬럼 명이 중복된다. 그래서 **@AttributeOverrides** 와 **@@AttributeOverride**를 사용해서 컬럼 명 속성을 재정의 해야한다.

```java
@Entity
public class Member {
    @Id
    @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;

    @Column(name = "USERNAME")
    private String username;

    @Embedded
    private Period period

    @Embedded
    private Address homeAddress;

    @Embedded
    @AttributeOverrides({
        @AttributeOverride(name="city", column=@Column(name = "WORK_CITY")),
        @AttributeOverride(name="street", column=@Column(name = "WORK_STREET")),
        @AttributeOverride(name="zipcode", column=@Column(name = "WORK_ZIPCODE"))
    })
    private Address workAddress;
}
```

## 4. 값 타입과 불변 객체

값 타입은 복잡한 객체 세상을 조금이라도 단순화하려고 만든 개념이다. 따라서 값 타입은 단순하고 안전하게 다룰 수 있어야 한다.

#### 값 타입 공유 참조

임베디드 타입 같은 값 타입은 여러 엔티티에서 참조하면 위험하다. 예를 들어서, 회원1과 회원2가 같은 주소를 참조하고 있다면 이 주소가 변경될 때, 둘 다 변경되는 문제점이 발생한다. 그렇기 때문에 별도로 가져가는 것이 좋다. 그래서 만약 같은 것을 사용하고 싶다면 복사해서 사용하는 것이 좋다.

#### 객체 타입의 한계

항상 값을 복사해서 사용하면 공유 참조로 인해 발생하는 부작용은 피할 수 있다. 문제는 임베디드 타입처럼 직접 정의한 값 타입은 자바의 기본 타입이 아니라 객체 타입니다. 객체 타입은 참조 값을 직접 대입하는 것을 막을 방법이 없다. 그래서 불변 객체로 만들어서 사용하는 것이 좋다.

#### 불변 객체

객체 타입을 수정할 수 없게 만들면 부작용을 원천 차단할 수 있다. 값 타입은 불변 객체로 설계해야한다. 생성 시점 이후 절대 값을 변경할 수 없는 객체가 불변 객체다. 생정자로만 값을 설정하고 수정자(Setter)를 만들지 않으면 된다 (수정자를 private으로 만들어도 된다.). 예를 들어서, Integer와 String이 자바가 제공하는 불변 객체다.

<br>

## 5. 값 타입 비교

값 타입은 인스턴스가 달라도 그 안에 값이 같으면 같은 것으로 봐야한다. 예를 들어, 아래의 두 **Address**는 같아야한다.

```java
Address A = new Address("A");
Address A1 = new Address("A");
```

그렇기 때문에 동등성을 보장해야한다.

> 동등성 비교 : 인스턴스의 값을 비교, equals() 사용  
> 동일성 비교 : 인스턴스의 참조 값을 비교, == 사용

적절한 **equals()** 메소드를 오버라이드 해줘야한다. 기본적으로 생성해주는 **equal()** 메소드를 사용하는 것이 좋다.

```java
@Embeddable
public class Address {
    private String city;
    private String street;
    private String zipCode;

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return fasle;
        Address address = (Address) o;
        return Objects.equals(city, address.city) &&
               Objects.equals(street, address.street) &&
               Objects.equals(zipcode, address.zipcode);
    }

    @Override
    public int hashCode() {
        return Objects.hash(city, street, zipcode);
    }
}
```

**equals()** 를 재정의 할때는 **hashCode()** 를 같이 재정의 해줘야한다. 그래야 **hashMap()** 과 같은 **hash**
컬렉션을 사용할 때, 동등성이 보장되면 해쉬 주소값에 의해서 같은 것으로 보장된다.

## 6. 값 타입 컬렉션

컬렉션을 별도의 테이블로 만들어준다. 

```java
@Entity
public class Member {
    @Id
    @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;

    @Column(name = "USERNAME")
    private String username;

    @Embedded
    private Period period

    @Embedded
    private Address homeAddress;

    @ElementCollection
    @CollectionTable(name = "FAVORITE_FOOD", joinColumns =
        @JoinCoulmn(name = "MEMBER_ID")
    )
    @Column(name = "FOOD_NAME")
    private Set<String> favoriteFoods = new HashSet<>();

    @ElementCollection
    @CollectionTable(name = "ADDRESS", JoinCoulmns =
        @JoinColumn(name = "MEMBER_ID")
    )
    private List<Address> addressHistory = new ArrayList<>();

}
```    
