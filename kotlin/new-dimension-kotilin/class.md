# class

## 1. 키워드

```kotlin
class Invoice(data:Int) {

}
```

- 클래스는 class 키워드로 선언한다.
  - 클래스 이름
  - 클래스 헤더 (형식 매개변수, 기본 생성자 등)
  - 클래스 바디 (중괄호 { })

```kotlin
class Empty
```

- 헤더와 바디는 옵션이고, 바디가 없으면 { }도 생략가능하다.

<br>

## 2. 생성자

Koltin에서는 생성자가 **기본 생성자**와 **보조 생성자**로 나뉜다.

```kotlin
class Person constructor(firstName: String) {

}
```

- 클래스 별로 1개만 가질 수 있다.
- 클래스 헤더의 일부이다.
- 클래스 이름 뒤에 작성한다.

```kotlin
class Person(firstName: String) {

}
```

- 클래스에 어노테이션이나 접근지정자가 없을 경우 기본생성자의 constructor 키워드를 생략할 수 있다.

```kotlin
class Customer(name: String) {
    init {
        logger.info("Customer initialized with value ${name}")
    }
}
```

- 기본 생성자는 코드를 가질 수 없다.
- 초기화는 초기화(init) 블록 안에서 작성해야한다.
- 초기화 블록은 init 키워드로 작성한다.
- 기본생정자의 파라매터는 init 블록 안에서 사용이 가능하다.
  

```kotlin
class Customer(name: String) {
    val customerKey = name.toUpperCase()
}
```

- 기본 생성자의 파라매터는 프로퍼티 초기화 선언에도 사용이 가능하다.

```kotlin
class Person(val firstName: String, val lastName: String) {

}
```

- 프로퍼티 선언 및 초기화는 기본 생성자에서 간결한 구문으로 사용 가능하다.

```kotlin
class Customer public @Inject constructor(name: String) { ... }
```

- 기본생성자에 어노테이션 접근 지정자 등이 있는 경우 constructor 키워드가 필요하다.

```kotlin
class Persion {
    constructor(parent: Person) {
        parent.children.add(this)
    }
}
```

- 보조 생성자를 클래스 별로 여러 개를 가질 수 있다.
- **constructor** 키워드로 선언한다.


```kotlin
class Person(val name: String) {
    constructor(name: String, parent: Person): this(name) {
        //...
    }

    constructor(): this("홍길동", Person()) {
        //...
    }
}
```

- 클래스가 기본 생성자를 가지고 있다면, 각각의 보조 생성자들은 기본 생성자를 직접 or 간접적으로 위임해 주어야 한다.

- this 키워드를 이용
  - 직접적 : 기본생성자에 위임
  - 간접적 : 다른 보조 생성자에 위임


```kotlin
class DontCreateMe {

}
```

- 클래스에 기본 생성자 or 보조 생성자를 선언하지 않으면 생성된 기본 생성자가 만들어진다.
- generated primary constructor
  - 매개변수가 없음
  - 가시성이 public임

```kotlin
class DontCreateMe private constructor() {

}
```

- 만약 생성된 기본 생성자의 가시성이 public이 아니어야 한다면, 다른 가시성을 가진 빈 기본 생성자를 선언해야한다.

```kotlin
val invoice = Invlice()

val customer = Customer("Joe Smith")
```

- 객체를 생성하려면 생성자를 일반 함수처럼 호출하면 된다.
- 코틀린은 new 키워드가 없다.