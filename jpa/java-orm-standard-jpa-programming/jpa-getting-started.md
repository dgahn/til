# JPA 시작하기

## 1. 데이터베이스 설치하기

#### MacOS 기준으로 설명합니다.


**h2** 데이터베이스를 설치합니다.

```bash
$ brew install h2
```

<br>

**h2** 데이터베이스를 실행합니다.

```bash
$ brew services start h2
```

<br>
아래의 Url에 접속하여 데이터베이스를 확인해야합니다.

```
http://localhost:8082
```

<br>

## 2. 프로젝트 생성하기

**gradle** 프로젝트를 생성합니다. (강의에서는 **maven**을 사용합니다.)

아래의 두가지 의존성을 추가합니다.

```gradle
compile group: 'org.hibernate', name: 'hibernate-entitymanager', version: '5.4.6.Final'
// jpa 인터페이스의 구현체인 하이버네이트를 의존성으로 추가했다.
compile group: 'com.h2database', name: 'h2', version: '1.4.199'
// 데이터베이슷 접근할 수 있는 h2 드라이버를 추가하였다.
```

<br>

## 3. JPA 설정

JPA을 실행시키위한 설정 파일인 **persistence.xml** 파일을 추가해야한다.

**persistence.xml** 파일은 위치가 표준 위치가 존재하는데 **src/main/resources/META-INF** 디렉터리에 넣어야 한다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<persistence xmlns="http://xmlns.jcp.org/xml/ns/persistence"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" version="2.2"
    xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_2.xsd">

    <persistence-unit name="jpabook">

        <properties>
            <!-- 필수 속성 -->
            <property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
            <property name="javax.persistence.jdbc.user" value="sa"/>
            <property name="javax.persistence.jdbc.password" value=""/>
            <property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"/>
            <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect" />

            <!-- 옵션 -->
            <property name="hibernate.show_sql" value="true" />
            <property name="hibernate.format_sql" value="true" />
            <property name="hibernate.use_sql_comments" value="true" />
            <property name="hibernate.id.new_generator_mappings" value="true" />

            <!--<property name="hibernate.hbm2ddl.auto" value="create" />-->
        </properties>
    </persistence-unit>

</persistence>
```

- **persistence** 태그의 **version** : JPA의 버전을 의미한다.
- **persistence-unit** 태그의 **name** : EntityManagerFactory를 나누는 기준이 된다. 보통 하나의 데이터베이스 단위라고 생각하면 된다.
- **필수 속성** : 데이터베이스에 대한 접근 정보가 필요하다.
- **property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"** : 데이터베이스마다 고유의 문법이 있는데 JPA가 알아서 해석해줄 때, 어떤 베이터베이스인지 JPA에게 알려준다.
- 속성 네임의 시작이 **javax**로 시작하는 것은 **JPA** 표준이기 때문이고 **hibernate**로 시작하는 것은 하이버네이트만의 고유 속성을 의미한다.

<br>

## 4. JPA 구동 방식

## 4.1 구동 순서 요약

1. JPA에 존재하는 **Persistence** 클래스가 **persistence.xml**을 통해서 **JPA** 설정 정보를 읽어간다.
2. **Persistence**는 **JPA** 설정 정보를 기반으로 **EntityManagerFactory** 객체를 생성한다.
3. **EntityManagerFactory**는 필요할 때마다 **EntityManager**를 생성한다.

<br>

## 4.2 동작 확인하기

메인클래스를 만들고 아래와 같이 메인 메소드를 작성해보자.

```java
public class JpaMain {

  public static void main(String[] args) {
    EntityManagerFactory emf = Persistence.createEntityManagerFactory("jpabook");
  }

}
```

**Persistence**로부터 **EntityManagerFactory**를 생성하는데 인자 값으로 **persistenceUnitName**을 넘겨야 한다. 이 **persistenceUnitName**은 **persistence.xml** 파일의 **persistence-unit** 태그의 **name**이 된다.

<br>

```java
public static void main(String[] args) {
    // persistence unit name을 넘기라고 한다.
    EntityManagerFactory emf = Persistence.createEntityManagerFactory("jpabook");

    EntityManager em = emf.createEntityManager();
    // 코드 작성
    em.close();

    // 애플리케이션이 꺼지면 emf를 닫는다.
    emf.close();
}
```

앞서 말했듯이 **Persistence**을 통해서 **EntityManagerFactory**을 생성하고 **EntityManagerFactory**을 통해서 **EntityManager**을 생성한다. **EntityManager** 생성하면 실제로 **JPA**를 이용하여 코드를 작성할 수 있다.

**EntityManagerFactory**는 애플리케이션을 로딩하는 시점에 데이터베이스마다 딱 한개만 만들어야한다. 그리고 실제로 데이터베이스의 한개의 트랜잭션마다 **EntityManager**를 한개씩 만들어야한다.

<br>

코드를 작성해서 데이터를 다루기 위해서는 먼저 데이터베이스에 테이블을 생성해야한다. 

```sql
CREATE TABLE member (
    id    BIGINT NOT NULL,
    name  VARCHAR(255),
    PRIMARY KEY(id)
);
```

<br>

그리고 **Member** 클래스를 만들어서 테이블과 매핑을 해보자.

```java
import javax.persistence.Entity;
import javax.persistence.Id;


@Entity
public class Member {

  @Id // Java에게 PK가 무엇인지 알려줘야한다.
  private Long id;
  private String name;

  Long getId() {
    return id;
  }

  void setId(final Long id) {
    this.id = id;
  }

  String getName() {
    return name;
  }

  void setName(final String name) {
    this.name = name;
  }

}
```

**@Enity** 애노테이션을 통해 **JPA**에게 해당 클래스가 데이터베이스와 연관되어야 됨을 알린다. 이 때, 반드시 **@Id**를 통해서 반드시 **PrimaryKey**가 무엇인지 알려줘야한다.
(애노테이션은 반드시 **javax.persistence** 패키지에 있는 것을 가져와서 사용해야한다.)

<br>

메인 메소드를 아래와 같이 수정하자.

```java
  public static void main(String[] args) {
    // persistence unit name을 넘기라고 한다.
    EntityManagerFactory emf = Persistence.createEntityManagerFactory("jpabook");

    EntityManager em = emf.createEntityManager();

    EntityTransaction tx = em.getTransaction();
    tx.begin();

    Member member = new Member();
    member.setId(1L);
    member.setName("dgahn");

    em.persist(member);

    tx.commit();

    em.close();

    emf.close();
  }
```

**EntityManager**를 통해서 트랜잭션을 가져올 수 있다. **JPA**에서는 반드시 트랜잭션 단위로 SQL를 질의해야하기 때문에 **EntityManager**을 통해서 **EntityTransaction**을 가져와야한다. 그리고 **tx.begin()**과 **tx.commit()** 사이에서 원하는 SQL 로직을 수행한다.

<br>

결과는 아래와 같이 나온다.

```
INFO: HHH000490: Using JtaPlatform implementation: [org.hibernate.engine.transaction.jta.platform.internal.NoJtaPlatform]
Hibernate: 
    /* insert io.github.dgahn.jpastarted.member.Member
        */ insert 
        into
            Member
            (name, id) 
        values
            (?, ?)
10월 03, 2019 12:39:59 오전 org.hibernate.engine.jdbc.connections.internal.DriverManagerConnectionProviderImpl$PoolState stop
INFO: HHH10001008: Cleaning up connection pool [jdbc:h2:tcp://localhost/~/test]
```

이렇게 자세히 나오는 이유는 **persistence.xml**의 설정 때문이다.

```xml
<property name="hibernate.show_sql" value="true" />
<property name="hibernate.format_sql" value="true" />
<property name="hibernate.use_sql_comments" value="true" />
<property name="hibernate.id.new_generator_mappings" value="true" />
```

- **hibernate.show_sql** : 실행된 SQL이 보인다.
- **hibernate.format_sql** : SQL의 포맷을 이쁘게 보여준다.
- **hibernate.use_sql_comments** : SQL이 왜 실행됬는지에 대한 코멘트를 보여준다.

<br>

테이블에 대한 이름을 변경하고자 할 때는 **@Table**을 사용하면 된다.

```java
@Entity
@Table(name="member")
public class Member {
}
```

하지만 관례적으로 **@Enity**를 선언하면 **Member**를 **member**로 인식한다. 그러므로 이 예제에는 굳이 적을 필요 없다.

<br>

마찬가지로 컬럼에 대한 이름을 변경하고자 할 때는 **@Column**을 사용하면 된다.

```java
@Entity
public class Member {

  @Id 
  private Long id;
  @Column(name = "name")
  private String name;
}
```

**@Column**의 이름에 대한 관례는 **@Entity**와 동일하다.

<br>

트랜잭션을 관리하기 위해서는 일반적으로 아래와 같이 작성한다. 아래 예제는 삽입을 위한 코드이다.

```java
public static void main(String[] args) {
  EntityManagerFactory emf = Persistence.createEntityManagerFactory("jpabook");

  EntityManager em = emf.createEntityManager();

  EntityTransaction tx = em.getTransaction();
  tx.begin();

  try {
    Member member = new Member();
    member.setId(3L);
    member.setName("dgahn2");
    em.persist(member);
    tx.commit();
  } catch (Exception e) {
    tx.rollback();
  } finally {
    em.close();
  }

  emf.close();
}
```

**EntityManager**은 트랜잭션 단위마다 사용하는 것이 리소스를 관리하는데 효율적이다. 질의가 끝나는 순간 데이터베이스 연결을 끊기 위해 닫아주는 것이 좋다.

<br>

조회를 위한 코드는 아래와 같다.

```java
try {
  Member findMember = em.find(Member.class, 1L);
  System.out.println("findMember.id = " + findMember.getId());
  System.out.println("findMember.name = " + findMember.getName());
  tx.commit();
  } catch (Exception e) {
    tx.rollback();
  } finally {
    em.close();
}
```

마치 **Java Collection**을 사용하는 것처럼 사용하는 것이 특징이다.

<br>

수정을 위한 코드는 아래와 같다.

```java
try {
  Member findMember = em.find(Member.class, 1L);
  findMember.setName("Hello, dgahn");
  tx.commit();
  } catch (Exception e) {
    tx.rollback();
  } finally {
    em.close();
}
```

수정 코드도 마치 **Java Collection**을 사용하듯이 작성하는데 이는 **JPA**가 가져온 객체에 대해서 관리하기 때문이다. **tx.commit()**하는 순간에 변경된 것이 있는지 확인하고 **UPDATE** SQL를 실행한다.

<br>

주의 해야할 점

- **EntityManagerFactory**는 애플리케이션마다 딱 하나만 만들어서 사용해야한다.
- **EntityManager**는 쓰레드간에 공유를 하면 안된다. 반드시, 한번 사용하고 버려야한다.
- **JPA의 모든 데이터 변경**은 하나의 트랜잭션에서 실행해야한다.

<br>

## 4.3 JPQL 소개

복잡한 쿼리를 **JPA만의 방식**으로 작성할 수 있게 하는 기능이다. 특징은 **SQL**을 **테이블** 대상이 아닌 **객체**를 대상으로 둔다.

아래는 예제 코드이다.

```java
try {
  List<Member> resultList = em.createQuery("select m from Member as m", Member.class)
                              .getResultList();

  for(Member member: resultList) {
    System.out.println("member.name = " + member.getName());
  }
  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

**createQuery**를 통해서 **JPQL**을 작성할 수 있는데 앞서 말했듯이 객체를 대상으로 쿼리를 작성한다. 그러면 **JPA**가 해당 **JPQL**에 대해서 실제 쿼리를 아래와 같이 변경해준다.

```
INFO: HHH000490: Using JtaPlatform implementation: [org.hibernate.engine.transaction.jta.platform.internal.NoJtaPlatform]
Hibernate: 
    /* select
        m 
    from
        Member as m */ select
            member0_.id as id1_0_,
            member0_.name as name2_0_ 
        from
            member member0_
member.name = Hello, dgahn
member.name = dgahn1
member.name = dgahn2
```

<br>

이에 대한 이 점은 데이터베이스마다 같은 기능이어도 문법이 다른 경우에 유용하다. 대표적인 예시가 페이징이다. 페이징에 대해서 **Mysql**은 **limit, offset**을 지원하는 반면 **Oracle**은 **rownum**을 사용한다. 

```java
List<Member> resultList = em.createQuery("select m from Member as m", Member.class)
                                  .setFirstResult(1)
                                  .setMaxResults(2)
                                  .getResultList();
```

이런 미세한 차이를 **JPA**가 알아서 변경해준다. 그에 대한 내용은 위에 **3. JPA 설정**을 다시 확인하자.

<br>

## 5. 용어 정리

내일 복습하면 작성한다.