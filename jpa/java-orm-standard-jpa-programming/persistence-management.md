# 영속성 관리 - 내부 동작 방식

## 1. EntityManagerFactory와 EntityManager

외부로부터 어떤 요청이 들어오면 **EntityManagerFactory**는 **EntityManager**를 생성한다. 이 생성은 요청마다 한개씩 생성한다. **EntityManager**는 내부적으로 커넥션 풀에서 커넥션을 하나 가져가서 데이터베이스와 연결을 한다.

<br>

## 2. 영속성 컨테스트란?

영속성 컨테스트란 엔티티를 영구 저장하는 환경을 의미한다. 아래의 코드를 보자.

```java
EnityManager.persis(entity);
```

이전 예제에서는 위의 코드가 **JPA**를 통해서 **entity**를 저장한다는 의미로 설명하였다. 하지만, 실제로는 **entity**를 **영속성 컨텍스트**에 저장하는 것이다.
영속성 컨테스트는 논리적인 개념으로 눈에 보이지 않는다. 단지, **EnityManager**를 통해서 **영속성 콘테스트**를 접근할 수 있는 것이다.

<br>

## 3. 엔티티의 생명주기

엔티티라는 **JPA**에 의해 관리되는 객체를 의미한다.

<br>

#### 비영속(new/transient)

아직 **JPA**에 의해서 관리되지 않은 **새로운** 상태를 의미한다. 코드로 보면 아래와 같다.

```java
Member member = new Member();
member.setId(1L);
member.setName("dgahn")
```

**JPA**와는 전혀 관련 없는 것을 알 수 있다.

<br>

#### 영속(managed)

`EnityManager.persis(entity)`를 통해서 **영속성 컨텍스트**에 관리되고 있는 상태를 의미한다. 코드로 보면 아래와 같다.

```java
//객체를 영속성 컨텍스트에 저장하지 않은 상태(비영속)
Member member = new Member();
member.setId(1L);
member.setName("dgahn");

EntityManager em = emf.createEntityManager();
em.getTransaction().begin();


// 객체를 영속성 컨텍스트에 저장한 상태(영속)
em.persist(member);
em.getTransaction().commit();
```

`em.persist(member)`가 실행되면 바로 SQL이 생성되서 DB에 값이 저장되는 것이 아니다. 실제로는 `em.getTransaction().commit();`이 호출되면 **SQL**이 생성되어 데이터베이스에 값이 저장된다.

<br>

#### 준영속(detached)

**영속성 컨텍스트**에서 해당 엔티티를 삭제한다.

```java
em.detach(member);
```

<br>

#### 삭제(removed)

실제 데이터베이스에서 객체를 삭제한 상태를 의미한다.

```java
em.remove(memeber);
```

<br>

## 3. 영속성 컨텍스트의 이점

#### 1차 캐시

영속성 컨텍스트를 캐시로 두고 영속성 컨텍스트에서 조회할 수 있는 값이면 영속성 컨텍스트에서 값을 가져올 수 있다. 코드를 보자.

```java
Member member = new Member();
member.setId(1L);
member.setName("dgahn");

// 1차 캐시에 저장됨
em.persist(member);

// 1차 캐시에서 조회
Member findMember = em.find(Member.class, 1L);
```

1차 캐시에서 먼저, 값을 조회해서 있으면 데이테베이스를 조회하지 않아도 된다. 만약, 1차 캐시에 없는 경우에는 데이터베이스에서 조회하는데 데이터베이스에 조회한 값도 1차 캐시에 저장이 된다.

하지만 1차 캐시는 하나의 트랜잭션 안에서만 효과가 있다.

<br>

#### 영속 엔티티의 동일성 보장

코드로 보는 것이 이해가 더 잘된다.

```java
Member member1 = em.find(Member.class, 1L);
Member member2 = em find(Member.class, 1L);

// member1 == member2
```

영속성 콘텍스트에서 값을 불러오는 것이기 때문에 위 두개의 객체가 동일하다는 것을 보장해준다.

<br>

#### 트랜잭션을 지원하는 쓰기 지원 (transactional write-behind)

```java
EntityManager em = emf.createEntityManager();
EntityTransaction tx = em.getTransaction();

// 데이터 변경시 트랜잭션을 시작해야한다.
transaction.begin();

// 영속성 컨텍스트에 값을 저장하는 것이지 실제 SQL을 데이터베이스에 보내는 것이 아니다.
em.persist(member1);
em.persist(member2);
 
// Commit하는 순간 데이터베이스 INSERT SQL를 보낸다.
tx.commit();
```

`persist(memberA)` 메소드를 호출하면 1차 캐시에 **memberA**를 저장하고 **쓰기 지연 SQL 저장소**라는 곳에 **INSERT SQL**이 저장된다. `persist(memberB)`도 마찬가지로 실행이되고
`tx.commit();`이 호출되는 순간에 **쓰기 지연 SQL 저장소**에 저장된 SQL이 데이터베이스로 **flush**된다. 그리고 마침내 실제 데이터베이스에서 **Commit**이 이루어진다.

이에 따른 가장 큰 장점은 SQL를 일괄 처리할 수 있다는 것이다. **JPA**에서는 일괄처리하는 사이즈를 조절할 수 있는데 **persistence.xml**에 아래와 같은 옵션을 통해 수정할 수 있다.

```xml
<property name="hibernate.jdbc.batch_size" value="10" />
```

10개의 SQL을 모아서 데이터베이스에 저장한다는 의미한다.

<br>

#### 변경 감지 (Dirty Checking)

```java
Member member = em.find(Member.class, 1L);
member.setName("dgahn");

// em.update(member) 같은 코드가 필요하지 않다.
```

1차 캐시에 저장된 값이 변경되면 JPA가 알아서 감지한 다음에 데이터베이스에 UPDATE SQL를 flush 한다.

이게 가능한 동작 원리는 아래와 같다.

1. 영속성 컨텍스트에는 데이터베이스나 **persist**한 최초의 값을 **스냅샷**으로 저장해둔다.
2. 그리고 **Entity**(최초의 값이 아닌 현재값)를 별도로 저장해두는데 **스냅샷**과 **Entity**을 비교하여 다른 것에 대해서 UPDATE SQL을 생성하여 **쓰기 지연 SQL 저장소**에 저장한다.
3. 그리고 실제 데이터베이스에 업데이트 한다.

<br>

## 4. 플러시(flush)

영속성 컨텍스트의 변경내용을 데이터베이스에 반영하는 행위를 의미한다. 즉, **쓰기 지연 SQL 저장소**의 SQL들을 데이터베이스 전송한다.

플러시가 발생하는 절차에 대해서 다시 설명하면

1. 1차 캐시에 존재하는 엔티티의 값이 변경됨을 감지한다.
2. 변경된 엔티티에 대해서 쓰기 지연 SQL 저장소에 SQL을 등록한다.
3. 쓰지 지연 SQL 저장소의 쿼리를 데이터베이스에 전송한다. (등록, 수정, 삭제 쿼리)

영속성 컨텍스트를 플러스하는 방법은 아래와 같다.

- em.flush() : 직접 호출
- tx.commit() : 플러시 자동 호출
- JPQL 쿼리 실행 : 플러시 자동 호출


플러시는 영속성 컨텍스트의 내용을 지우지 않는다. 플러시의 중요한 역할은 영속성 컨텍스트의 변경 내용을 데이터베이스에 동기화하는 것에 있다.

<br>

## 5. 준영속 상태

영속 상태의 엔티티가 영속성 컨텍스트로부터 분리되는 것을 의미한다. 그러므로 영속성 컨텍스트가 제공하는 기능을 사용할 수가 없다.

준영속 상태로 만드는 대표적인 방법은 아래 3가지가 있다.

- em.detach(entity) : 특정 엔티티만 준영속 상태로 만든다.
- em.clear() : 영속성 컨텍스트를 완전히 초기화한다.
- em.close() : 영속성 컨텍스트를 종료한다.

<br>

## 용어 정리

내일 복습해서 정리한다.

<br>

**주의할 점** 
- JPA를 사용하는 클래스라면 기본 생성자를 만드시 만들어야한다.