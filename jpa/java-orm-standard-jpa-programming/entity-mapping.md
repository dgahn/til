# 엔티티 매핑

## 1. 엔티티 매핑 소개

JPA에서 자바의 요소들과 데이터베이스의 요소들간에 아래와 같은 매핑이 있다.

- 객체와 테이블 매핑 : @Enity, @Table
- 필드와 컬럼 매핑 : @Column
- 기본키 매핑 : @Id
- 연관관계 매핑 : @ManyToOne, @JoinColumn

<br>

### 1.1 @Entity

이 애노티에션이 붙은 클래스는 JPA가 관리하는 객체가 되고 엔티티라고 부른다. JPA를 사용해서 테이블과 매핑할 클래스는 @Entity가 필수다.

주의할 점은 **기본 생성자**가 필수고 **fianl, enum, interface, inner** 클래스는 사용할 수 없다. 그리고 저장할 필드에도 **final**을 사용할 수 없다.

<br>

### 1.2 @Table

@Table은 엔티티와 매핑할 테이블을 지정한다. 속성으로는 아래와 같은 속성이 있다.

|           속성           | 기능                     | 기본값        |
| :--------------------: | :--------------------- | :--------- |
|          name          | 매핑할 테이블 이름             | 엔티티 이름을 사용 |
|        catalog         | 데이터베이스 catalog 매핑      |            |
|         schema         | 데이터베이스 schema 매핑       |            |
| uniqueConstraints(DDL) | DDL 생성 시에 유니크 제약 조건 생성 |            |

코드를 보면 아래와 같다.

```java
@Entity
@Table(name = "MBR")
public class Member {
}
```

<br>

## 2. 데이터베이스 스키마 자동 생성

JPA에서는 애플리케이션 로딩 시점에 **DDL**을 자동으로 생성할 수 있다. 번거러운 작업을 JPA가 해준다는 장점이 있다. 그리고 데이터베이스 방언을 활용해서 데이터베이스에 맞는 적절한 DDL를 생성한다. 단, 개발 장비에서만 사용해야한다.

**persistence.xml** 파일에 아래와 같은 옵션을 추가하면 된다.

```xml
<property name="hibernate.hbm2ddl.auto" value="create" />
```

객체에 속성이 추가되면 자동으로 테이블을 삭제하고 변경된 테이블 구조로 테이블을 만든다. 그렇기 때문에 데이터베이스를 별도로 수정할 필요가 없다.

속성 **value**는 아래의 옵션들을 넣을 수 있다.

| 옵션          | 설명                                                     |
| :---------- | :----------------------------------------------------- |
| create      | 기존 테이블 삭제 후 다시 생성(DROP + CREATE)                       |
| create-drop | create와 같으나 종료시점에 테이블 DROP, 테스트한 것을 깔끔하게 지울 때 사용하기 좋다. |
| update      | 변경분만 반영(운영 DB에는 사용하면 안됨)                               |
| validate    | 엔티티와 테이블이 정상 매핑되었는지만 확인                                |
| none        | 사용하지 않음                                                |

테이블을 자동으로 생성해줄 때, **@Column**의 속성을 추가하여 **not null**, **unique**등을 추가할 수 있다. 해당 기능은 JPA를 실행하는데 있어서는 영향을 주지 않고 DDL 생성에만 영향을 준다.

### 주의할 점

운영 장비에는 절대 create, create-drop, update를 사용하면 안된다. 개발 초기에는 create 또는 update를 사용하는 것이 좋고, 테스트 서버는 update 또는 validate를 추천한다. 그리고 스테이징과 운영 서버는 validate 또는 none을 사용한다.
김영한님은 자기 컴퓨터에서만 사용하고 그외에는 자신이 직접 DDL 작성을 하는 것을 추천한다고 한다.

<br>

## 3. 필드와 컬럼 매핑

코드를 보고 각 매핑 애노테이션의 특징을 알아보자.

```java

@Entity
public class Member {

    @Id
    private Long id;

    @Column(name = "name")
    private String username;

    private Integer age;

    @Enumerated(EnumType.STRING)
    private RoleType roleType;

    @Temporal(TemporalType.TIMESTAMP)
    private Date createdDate;

    @Temporal(TemporalType.TIMESTAMP)
    private Date lastModifiedDate;

    @Lob
    private String description;

    public Member() {
    }
}
```

- **@Column(name = "name")** : 자바 애플리케이션에서는 **username**을 쓰고 데이터베이스에서는 **name**을 쓰고 싶은 경우 **name** 속성을 통해서 데이터베이스의 컬럼 값을 지정하면 된다.
- **@Enumerated(EnumType.STRING)** : 자바의 Enum 타입을 사용하고 싶은 경우 사용하면 된다.
- **@Temporal(TemporalType.TIMESTAMP)** : 날짜를 사용하고 싶은 경우 사용하면 된다.
   - TemporalType.DATE : 날짜을 의미한다.
   - TemporalType.TIME : 시간을 의미한다.
   - TemporalType.TIMESTAMP : 날짜와 시간을 의미한다.
- **@Lob** : **VARCHAR** 타입을 넘는 큰 문자열을 넣을 경우 사용하면 된다.

다시 표로 정리하면 아래와 같다.

| 어노테이션       | 설명                       |
| :---------- | :----------------------- |
| @Column     | 컬럼 매핑                    |
| @Temporal   | 날짜 타입 매핑                 |
| @Enumerated | enum 타입 매핑               |
| @Lob        | BLOB, CLOB 매핑            |
| @Transient  | 특정 필드를 컬럼에 추가하지 않고 싶은 경우 |

<br>

#### 3.1 @Column

**@Column**에는 아래와 같은 속성이 있다.

|속성|설명|기본값|
|:---|:---|:---|
|name|필드와 매핑할 테이블의 컬럼 이름|객체의 필드 이름|
|insertable, updatable|데이터베이스에 해당 컬럼에 대한 값을 등록, 변경 가능 여부|TRUE|
|nullable(DDL)|null 값의 허용 여부를 설정한다. false로 설정하면 DDL 생성 시에 not null 제약조건을 걸 때 사용한다.||
|unique(DDL)|@Table의 uniqueConstraints와 같지만 한 컬럼에 간단히 유니크 제약조건을 걸 때 사용한다.||
|columnDefinition|데이터베이스 컬럼 정보를 직접 줄 수 있다. ex) varchar(100) default 'EMPTY'|필드와 자바 타입과 방언 정보를 사용해|
|length(DDL)|문자 길이 제약조건, String 타입에만 사용한다.|255|
|precision, scale(DDL)|BigDecimal 타입에서 사용한다(BigInteger도 사용할 수 있다). precision은 소수점을 포함한 전체 자 릿수를, scale은 소수의 자릿수 다. 참고로 double, float 타입에는 적용되지 않는다. 아주 큰 숫자나 정 밀한 소수를 다루어야 할 때만 사용한다.|precision=19, scale=2|

- unique는 제약 조건의 이름이 랜덤으로 생성되기 때문에 잘 사용하지 않고 **@Table**의 속성인 **???**을 주로 사용한다.

<br>

#### 3.2 @Enumerated

**@Enumerated**에는 아래와 같은 속성이 있다.

|속성|설명|기본값|
|:---|:---|:---|
|value|• EnumType.ORDINAL: enum 순서를 데이터베이스에 저장<br>• EnumType.STRING: enum 이름을 데이터베이스에 저장|EnumType.ORDINAL|

하지만 **ORDINAL**은 안쓰는 것이 좋다. 이유는 순서가 **enum** 클래스에 저장되어 있는 순서가 변경될 수 있기 때문이다. 그러므로 무조건 **EnumType.STRING**을 사용하자.

<br>

#### 3.3 @Temporal

날짜 타입(java.util.Date, java.util.Calendar)을 매핑할 때 사용한다.  
하지만 최근 **Java8**에서 지원하는 **LocalDate**, **LocalDateTime**을 사용할 때는 생략이 가능하다. 안써도 된다는 의미다.(최신 하이버네이트 지원)  
**LocalDate**는 년월, **LocalDateTime**은 년월시간을 의미한다.
 

|속성 |설명 |기본값|
|:---|:---|:---|
|value|• TemporalType.DATE: 날짜, 데이터베이스 date 타입과 매핑 (예: 2013-10-11) <br> • TemporalType.TIME: 시간, 데이터베이스 time 타입과 매핑 (예: 11:11:11) <br> • TemporalType.TIMESTAMP: 날짜와 시간, 데이터베이스 timestamp 타입과 매핑 (예: 2013–10–11 11:11:11)

<br>

#### 3.4 @Lob

데이터베이스 **BLOB, CLOB** 타입과 매핑이 가능하다. 이 애노테이션은 지정할 수 있는 속성이 없고 매핑하는 필드 타입이 문자면 **CLOB**, 나머지는 **BLOB**에 매핑한다.

- **CLOB** : String, char[], java.sql.CLOB
- **BLOB** : byte[], java.sql.BLOB

<br>

#### 3.5 @Transient

매핑을 하고 싶지 않는 경우 사용하면 된다.

<br>

## 4. 기본키 매핑

- **@Id** : 직접 할당하는 경우에 사용한다.
- **@GeneratedValue** : 자동 생성하는 경우 사용한다.

**@Id**의 경우 그냥 사용하면 되지만 **@GeneratedValue**의 경우에는 여러가지 전략 중에 하나를 선택해서 사용하면 된다.

<br>

## @GeneratedValue(stratege = GenerationType.AUTO)

```java
@GeneratedValue(stratege = GenerationType.AUTO)
private String id;
```

아래의 3가지 방식에 따라서 자동으로 설정된다. 언제든지 변경될 수 있으므로 확인을 하고 사용해야한다.

<br>

## @GeneratedValue(stratege = GenerationType.IDENTITY)

```java
@GeneratedValue(stratege = GenerationType.IDENTITY)
private String id;
```

데이터베이스 방언에 맞춰서 **id**가 생성된다. 예를 들어, **MySQL**의 경우 **AUTO INCREMENT**를 이용하여 자동으로 **ID**가 생성된다.

#### IDENTITY 전략의 특징

기본 키의 생성을 데이터베이스에 위임이 된다. 하지만 영속성 컨텍스트는 데이터베이스에 들어가기전에 1차 캐시에서 식별하기 위해서는 해당 기본키의 값을 알아야한다. 그래서 IDENTITY 전략에서는 **persist**을 호출하는 순간 커밋이 진행된다.


<br>

## @GeneratedValue(stratege = GenerationType.SEQUENCE)

```java
@GeneratedValue(stratege = GenerationType.SEQUENCE)
private Long id;
```

별도의 시퀀스를 명시하지 않으면 자동으로 데이터베이스마다 존재하는 **기본 시퀀스**를 사용한다. 하지만 보통 테이블마다 별개의 시퀀스를 사용하므로 아래와 같이 시퀀스를 생성하고 매칭시킬 수 있다.

```java
@Entity
@SequenceGenerator(name = "MEMBER_SEQ_GENERATOR", sequenceName = "MEMBER_SEQ", initalValue = 1, allocationSize = 1)
public class Member {
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "MEMBER_SEQ_GENERATOR")
    private Long id;
}
```

<br>


#### @SequenceGenerator 속성

| 속성              | 설명                                                                                      | 기본값                |
| :-------------- | :-------------------------------------------------------------------------------------- | :----------------- |
| name            | 식별자 생성기 이름                                                                              | 필수                 |
| sequenceName    | 데이터베이스에 등록되어 있는 시퀀스 이름                                                                  | hibernate_sequence |
| initialValue    | DDL 생성 시에만 사용됨, 시퀀스 DDL을 생성할 때 처음 1 시작하는 수를 지정한다.                                       | 1                  |
| allocationSize  | 시퀀스 한 번 호출에 증가하는 수(성능 최적화에 사용됨 **데이터베이스 시퀀스 값이 하나씩 증가호도록 설정되어 있으면 이 값을 반드시 1로 설정해야 한다.) | **50**             |
| catalog, schema | 데이터베이스 catalog, schema 이름                                                               |                    |

<br>

#### 시퀀스 전략의 특징

시퀀스 전략을 선택하게되면 실제 데이터베이스에서 시퀀스의 값을 가져와야한다. 그래서 **persist** 메소드를 호출하게 되면 **시퀀스 값**을 가져오게 된다. 그리고 영속성 컨텍스트에 **persist**하기 위한 엔티티를 넣는다. 그렇기 때문에 시퀀스 전략에서는 **persist** 하는데 있어 버퍼를 사용할 수 있다. 하지만, 시퀀스의 값을 **persist** 할 때마다 호출해야한다는 단점이 있다.

이를 해결하기 위해서 **allocation size**를 이용하면 된다. 이 속성의 경우 데이터베이스로부터 시퀀스를 미리 확보를 하여 **애플리케이션**에서 확보한 시퀀스를 이용하여 시퀀스를 사용할 수 있다.

예를 들어, 아래 코드를 보자.

```java
Member member1 = new Member();
member1.setUsername("A");

Member member2 = new Member();
member2.setUsername("B");

Member member3 = new Member();
member3.setUsername("C");

em.persist(member1);
em.persist(member2);
em.persist(member3);
```

- **IDENTITY** 전략을 사용하고 **persist()**하면 메소드를 호출할 때마다 데이터베이스에 **커밋**을 할 것이고 그 때마다 네트워크 비용이 들 것이다.
- **SEQUENCE** 전략을 사용하고 **allocationSize**를 **1**로 하고 **persist()**하면 그 때마다 데이터베이스에 시퀀스를 조회할 것이다.
- **SEQUENCE** 전략을 사용하고 **allocationSize**를 **50**로 하고 **persist()**하면 처음에 시퀀스를 **50**까지 확보한 한다. 이 때, 시퀀스 호출을 두번하는데 현재 자신이 가져오는 것과 다음에 가져올 때 값을 비교하여 자기한테 할당된 갯수를 다시 한번 확인하는 것 같다. 그리고 그 다음에 **persist()** 메소드가 호출되면 메모리에 있는 **시퀀스 현재 밸류**를 가져와 사용할 것이다. 시퀀스를 **50개** 확보하면 데이터베이스에서는 현재 밸류 51이 된다.

<br>

## @GeneratedValue(stratege = GenerationType.TABLE)

키 생성 전용 테이블을 하나 만들어서 데이터베이스 시퀀스를 흉내내는 전략이다. 모든 데이터베이스에 적용이 가능한다는 장점이 있지만 성능에 이슈가 있다.


```sql
CREATE TABLE my_sequences (
  sequence_name VARCHAR(255) NOT NULL,
  next_val BIGINT,
  PRIMARY KEY (sequence_name)
);
```

```java
@Entity
@TableGenerator(name = "member_seq_generator",
                table = "my_sequences",
                pkColumnValue = "member_seq", allocationSize = 1)
public class Member {
    @Id
    @GeneratedValue(strategy = GeneratedValue.TABLE,
                    generator = "member_seq_generator")
    private Long id;
}
```

데이터베이스에서 쓰는 관례들이 있기 때문에 잘 사용되지는 않는다.

<br>


## 권장하는 식별자 전략

기본키는 null이 아니고 유일하면 변하면 안된다. 비즈니스적으로 만족하는 키인 자연키는 찾기가 어렵다. 그렇기 때문에 권장은 **Long형 + 대체키(sequence, uuid, autoincrement) + 키** 생성전략을 사용하자.

<br>

## 용어 정리

내일 정리하면서 작성한다.