# Properties and Fields

## 1. 프로퍼티 선언

```kotlin
class Address {
    var name: String = "Kotlin" // var (mutable)
    val city: String = "Seoul" // val (read-only)
}
```

코틀린 클래스는 프로퍼티를 가질 수 있다.

```kotlin
fun copyAddress(address: Address): Address {
    val result = Address()
    result.name = address.name // .name 자체가 getName()을 호출하는 것이다.
    return result
}
```

<br>

프로퍼티 사용은 자바의 필드를 사용하듯이 하면 된다.

## 2. 프로퍼티 문법

### 2.1 전체 문법

```kotlin
var <propertyName>[:<PropertyType>] [=<property_initializer>]
    [<getter>]
    [<setter>]
```

옵션 (생략 가능)
- PropertyType
  - property_initializer에서 타입을 추론 가능한 경우 생략이 가능하다.
- property_initializer
- getter
- setter

<br>

### 3. 프로퍼티 예제

아래의 두 코드는 거의 같다.

```kotlin
class Address {
    var name = "Kotlin"
}
```

```kotlin
class Address {
    var name: String = "Kotlin"
        get() {
            return field
        }
        set(value) {
            field = value
        }
}
```

<br>

### 3.1 var (mutable) 프로퍼티

```kotlin
class Address {
    // default getter와 setter
    var intialized = 1

    // error: 오류 발생
    // default getter와 setter를 사용한 경우
    // 명시적인 초기화가 필요함
    var allByDefault: Int?
}
```

### 3.2 val(read-only) 프로퍼티

- var 대신 val 키워드를 사용한다.
- setter가 없다

```kotlin
class Address {
    // default getter
    // 타입은 Int
    val initialized = 1

    // error: 오류 발생
    // default getter
    // 명시적인 초기화가 필요하다.
    val allByDefault: Int?
}
```

<br>

## 4. Custom accessors (getter, setter)

custom accessor는 프로퍼티 선언 내부에, 일반 함수처럼 선언할 수 있다.

### 4.1 getter 재정의

```kotlin
val isEmpty: Boolean
    get() = this.size == 0
```

### 4.2 setter 재정의

관습적으로 setter의 파라미터의 이름은 value이다. (변경 가능하다.)

```kotlin
var stringRepresentation:String
    get() = this.toString()
    set(value) {
        setDataFromString(value)
    }
```

<br>

## 5. 타입 생략

코틀린 1.1부터는 getter를 통해 타입을 추론 할 수 있는 경우, 프로퍼티의 타입을 생략할 수 있다.

```kotlin
val isEmpty: Boolean
    get() = this.size == 0
```
```kotlin
val isEmpty
    get() = this.size == 0
```

<br>

## 6. 프로퍼티 가시성 및 어노테이션

```kotlin
var setterVisibility: String = "abc"
    private set
var setterWithAnnotation: Any? = null
    @Inject set
```

body 없는 accessor를 통해 가시성 변경이나 어노테이션을 붙일 수 있다.

```kotlin
var setterVisiblity: String = "abc"
    private set(value) {
        field = value
    }
```

body를 작성해 주어도 상관이 없다.

<br>

## 7. Backing Fields

field는 프로퍼티의 accessor에서만 접근 가능한 실질적인 프로퍼티를 의미한다.

```kotlin
var counter = 0
    set(value) {
        if(value >= 0) field = value // set의 body에서만 field를 접근할 수 있다.
    }
```

항상 backing field를 생성하는 것은 아니다.

```kotlin
var counter = 0
    set(value) {
        if(value >= 0) field = value
    }
```

accessor 중 1개라도 기본 구현을 사용하는 경우와 custom accessor에서 field 식별자를 참조하는 경우를 의미한다.

```kotlin
val isEmpty: Boolean
    get() = this.size == 0
```

위와 같은 경우는 backing field를 생성하지 않는다. 위의 경우 JVM에서 순수 메소드로 생성한다.

## 8. Backing Properties

```kotlin
private var _table: Map<String, Int>? = null
public val table: Map<String, Int>
       get() {
           if(_table == null) {
               _table = HashMap()
           }
           return table ?: throw AssertionError("null")
       }
```

"implicit backing field" 방식이 맞지 않는 경우에는 "backing property"를 이용할 수 있다.

위 예제 같은 경우 table을 읽기 전용으로 만들기 위해서 "backing property"을 이용한 예제이다.

<br>

## 9. Compile-Time Constants

const modifier를 이용하면 컴파일 타임 상수를 만들 수 있다. 이런 프로퍼티는 어노테이션에서도 사용 가능하다.

```kotlin
const val SUBSYSTEM_DEPRECATED: String = "This subsystem is deprecated"

@Deprecated(SUBSYSTEM_DEPRECATED)
fun foo() { ... }
```

Top-level이거나 object의 멤버이거나 String이나 primitive 타입으로 초기화된 경우 사용할 수 있다.

<br>

## 10. Late-Initialzed Properties

일반적으로 프로퍼티는 non-null 타입으로 선언된다. 간혹 non-null 타입 프로퍼티를 사용하고 싶지만, 생성자에서 초기화를 해줄 수 없는 경우가 있다.

- Dependency Injection
- Butter knife
- Unit test의 setup 메소드

```kotlin
public class MyTest {
    lateinit var subject: TestSubject // lateinit 키워드를 통해서 이 프로퍼티가 나중에 초기화될 것을 알려준다.

    @SetUp fun setup() {
        subject = TestSubject()
    }

    @Test fun test() {
        subject.method()
    }
}
```

### 10.1 lateinit를 사용할 수 있는 조건

- 클래스의 바디에서 선언된 프로퍼티만 가능하다.
- 기본 생성자에서 선언된 프로퍼티는 안된다.
- var 프로퍼티만 가능하다.
- custom accessor가 없어야한다.
- non-null 타입이어야 한다.
- 프리미티브 타입이면 안된다.