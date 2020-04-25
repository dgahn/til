# 코틀린으로 함수형 프로그래밍 시작하기

코틀린 문법을 알아보자

## 2.1 프로퍼티 선언과 안전한 널 처리

프로퍼티 선언과 널 처리기능에 대해서 알아본다.

### 프로퍼티 선언

읽기 전용 프로퍼티를 선언할 수 있다. **val**는 자바의 final로 선언한 변수와 유사하다.

```kotlin
val value: Int = 10
```

가변 프로퍼티를 선언할 수 있다. **var**는 선언 이후에 수정이 가능한 가변 프로퍼티로 사용할 수 있다.

```kotlin
var variable = 10
```

코틀린은 프로퍼티에 할당되는 값을 보고 타입을 추론할 수 있다.

```kotlin
val value = 10 // Int로 추론
var variable = "a" // String으로 추론 
```

### 안전한 널 처리

코틀린은 언어 차원에서 안전한 널처리 기능을 제공한다. 코틀린에서 타입 뒤에 ?를 붙이면 값으로 Null을 할당할 수 있다.

```kotlin
val nonNull: Int = null // 컴파일 오류 발생
val nullable: Int? = null
```

## 2.2 함수와 람다

### 함수를 선언하는 다양한 방법

코드 블록으로 선언, 반환 타입 **Unit**은 자바의 **void**와 유사하다.

```kotlin
fun twice1(value: Int): Int {
  return value * 2
}

fun twice2(value: Int): Unit {
    // ...
}
```

코드 블록과 return을 생략하고 선언할 수 있다.

```kotlin
fun twice3(value: Int): Int = value * 2
```

타입 추론이 가능하기 때문에 반환 타입을 생략할 수 있다.

```kotlin
fun twice4(value: Int) = value * 2
```

매개변수가 두 개인 경우 다음과 같이 선언할 수 있다.

```kotlin
fun add1(x: Int, y: Int) {
    return x + y
}

fun add2(x: Int, y: Int): Int = x + y

fun add3(x: Int, y: Int) = x + y
```

### 매개변수에 기본값 설정하기

코틀린은 함수의 매개변수에 기본 값을 설정할 수 있다.

```kotlin
fun add(x: Int, y: Int = 3) = x + y

println(add(9, 1)) // "10" 출력
println(add(10)) // "13" 출력
```

### 익명 함수와 람다 표현식

익명함수 : 함수 이름을 선언하지 않고 구현부만 작성하는 방식

코틀린은 람다식을 이용해서 익명 함수를 표현할 수 있다.

```kotlin
fun sum(x: Int, y: Int, calculate: (Int, Int) -> Int): Int {
    return calculate(x, y)
}

val value = sum(5, 10, { x, y -> x + y })
```

### 확장 함수

이미 작성된 클래스에 함수나 프로퍼티를 추가할 수 있다.

```kotlin
fun Int.product(value: Int): Int {
    return this * value
}

println(10.product(2))
```

## 2.3 제어 구문

### if 문

코틀린에서 if문은 기본적으로 **표현식**이다. 표현식은 구문과 달리 어떤 값을 반환한다. 다음과 같은 경우네는 **else**를 반드시 작성해야한다.

```kotlin
val max: Int = if (x > y) x else y
```

### when문

**when**문은 **switch**문과 비슷한 기능을 한다. 추가적으로 **표현식**이며 **패턴 매칭**이 가능하다. 복수의 값에 대한 조건을 만들 때는 ","로 구분한다. 

```kotlin
val number = when (x) {
    "일" -> 1
    "이", "2" -> 2
    3.toString() -> 3
}
```

조건문에 따라 분기 처리도 가능하다.

```kotlin
val numType = when {
    x == 0 -> "zero"
    x > 0 -> "positive"
    else -> "negative"
}
```

### for문

for문을 다양한 방식으로 사용할 수 있다.

```kotlin
val collection = listOf(1,2 ,3)

for (item in collection) {
    println(item) // "123" 출력
}

for ((index, item) in collection.withIndex()) {
    println("the element at $index is $item")
}
```

값의 범위와 증감 규칙을 설정할 수 있다.

```kotlin
for (i in 1..3) {
    print(i) // "123" 출력
}

for (i until 1..3) {
    print(i) // "12" 출력
}

for (i in 6 downTo 0 step 2) {
    print(i) // "6420" 출력
}
```

## 2.4 인터페이스

### 인터페이스의 특징

객체지향 언어에서 인터페이스는 클래스의 기능 명세다.

코틀린에서 제공하는 인터페이스는 다음과 같은 특징이 있다.

- 다중 상속이 가능하다.
- 추상 함수를 가질 수 있다.
- 함수의 본문을 구현할 수 있다.
- 여러 인터페이스에서 같은 이름의 함수를 가질 수 있다.
- 추상 프로퍼티를 가질 수 있다.

### 인터페이스 선언하고 상속하기

코틀린에서 **:** 을 통해서 상속을 표현한다. 여러 개의 인터페이스를 상속하기 위해서는 구분자로 **,** 를 사용한다.

```kotlin
interface Foo {

}

interface Bar {

}

class Kotlin: Foo, Bar {

}
```

### 인터페이스에 추상 함수 선언하기

인터페이스 선언하고 클래스에 상속하는 예다.

```kotlin
interface Foo {
    fun printFoo()
}

interface Bar {
    fun printBar()
}

class Kotlin: Foo, Bar {
    override fun printFoo() {
        // ...
    }

    override fun printBar() {
        // ...
    }
}
```

인터페이스의 추상 함수를 재정의할 때 **override** 키워드를 사용한다.

### 추상 함수 구현하기

인터페이스와 인터페이스를 상속한 클래스에 추상 함수를 구현할 수 있다.

```kotlin
fun main() {
    val kotlin = Kotlin()
    kotlin.printFoo() // "Foo" 출력
    kotlin.printBar() // "Kotlin - Bar" 출력
}

interface Foo {
    fun printFoo() {
        println("Foo")
    }
}

interface Bar {
    fun printBar() {
        println("Bar")
    }
}

class Kotlin: Foo, Bar {
    override fun printBar() {
        println("Kotlin - Bar")
    }
}
```

두 인터페이스에 같은 이름의 추상 함수가 선언되어 있는 경우는 **super<Foo>.printKotlin()**과 같은 방식으로 상속 받은 인터페이스의 함수를 선택해서 사용할 수 있다.

```kotlin
fun main() {
    val kotlin: Kotlin = Kotlin()
    kotlin.printBar()
    kotlin.printFoo()
    kotlin.printKotlin()
}

interface Foo {
    fun printFoo() {
        println("Foo")
    }

    fun printKotlin() {
        println("Foo Kotlin")
    }
}

interface Bar {
    fun printBar() {
        println("Bar")
    }

    fun printKotlin() {
        println("Bar Kotlin")
    }
}

class Kotlin: Foo, Bar {
    override fun printKotlin() {
        super<Foo>.printKotlin()
        super<Bar>.printKotlin()
    }
}
```

### 추상 프로퍼티의 선언과 사용

추상 프로퍼티란 해당 인터페이스를 상속한 클래스가 가질 프로퍼티를 말한다.

```kotlin
fun main() {
    val kotlin: Kotlin = Kotlin()
    println(kotlin.bar) // "3" 출력
}

interface Foo {
    val bar: Int

    fun printFoo() {
        println("Foo")
    }
}

class Kotlin: Foo {
    override val bar: Int = 3
}
```

인터페이스에서 추상 프로퍼티에 대해 초기 값을 설정할 수 있는데 **get()** 을 이용해서 구현해야한다.

```kotlin
fun main() {
    val kotlin: Kotlin = Kotlin()
    println(kotlin.bar) // "3" 출력
}

interface Foo {
    val bar: Int
        get() = 3

    fun printFoo() {
        println("Foo")
    }
}

class kotlin: Foo {
    
}
```

## 2.5 클래스

### 클래스와 프로퍼티

코틀린에서는 new 키워드 없이 객체를 생성한다. 클래스와 프로퍼티를 선언하는 방법은 다음과 같다.

```kotlin
class User(var name: String, val age: Int = 30)

val user = User("FP", 32)
println(user.name) // "FP" 출력
user.name = "kotlin"
println(username.name) // "kotlin" 출력

val user2 = User("FP")
println(user2.adg) // "18" 출력
```

- val로 선언된 프로퍼티는 게터만 사용할 수 있다.
- var로 선언된 프로퍼티는 게터와 세터를 모두 사용할 수 있다.
- 클래스 생성자에 기본값을 지정할 수 있다.

### Data 클래스

data class는 기본적으로 hashCode, equals, toString과 같은 함수를 기본적으로 생성해준다.

```kotlin
data class Person(val firstName: String, val lastName: String, val age: Int)
```

### enum 클래스

enum 클래스는 상수에 이름을 붙여 주는 클래스다.

```kotlin
enum class Error(val num: Int) {

    WARN(2) {
        override fun getErrorName(): String {
            return "WARN"
        }
    },

    ERROR(3) {
        override fun getErrorName(): String {
            return "ERROR"
        }
    },

    FAULT(1) {
        override fun getError(): String {
            return "FAULT"
        }
    };

    abstract fun getErrorName(): String
}
```

### sealed 클래스

sealed 클래스는 클래스를 묶은 클래스다.

```kotlin
sealed class Expr
data class Const(val number: Double): Expr()
data class Sum(val e1: Expr, val e2: Expr): Expr()
object NotANumber: Expr()
```

같은 파일 내에 있는 클래스들을 묶어주기 때문에 클래스 패턴 매칭을 할 때 유용하게 사용할 수 있다.

```kotlin
fun eval(expr: Expr): Double = when(expr) {
    is Const -> expr.number
    is Sum -> eval(expr.e1) + eval(expr.e2)
    NotANumber -> Double.NaN
}
```

## 2.6 패턴 매칭

패턴 매칭이란 값, 조건, 타입 등의 패턴에 따라 매칭되는 동작을 수행할 수 있게 하는 기능이다.

### 다양한 패턴 정의 방법

값의 패턴에 따른 매칭

```kotlin
fun main() {
    println(checkValue("kotlin")) // "kotlin" 출력
    println(checkValue(5)) // "1..10" 출력
    println(checkValue(15))  "11 or 15" 출력
    println(checkValue(User("Joe", 76)))  // "User" 출력
    println(checkValue("unknown"))  // "SomeValue" 출력
}

data class User(val name: String, val age: Int)

fun checkValue(value: Any) when (value) {
    "kotlin" -> "kotlin"
    in 1..10 -> "1..10"
    11, 15 -> "11 or 15"
    is User -> "User"
    else -> "SomeValue"
}
```

조건에 따른 패턴 매칭

```kotlin
fun main() {
    println(checkCondition("kotlin")) // "kotlin" 출력
    println(checkCondition(5)) // "1..10" 출력
    println(checkCondition(User("Joe", 76))) // "== User(Joe, 76)" 출력
    println(checkCondition(User("Sandy", 65))) // "is User" 출력
    println(checkCondition("unknow")) "SomeValue" 출력
}

data class User(val name: String, val age: Int)

fun checkCondition(value: Any) = when {
    value == "kotlin" -> "kotlin"
    value in 1..10 -> "1..10"
    value === User("Joe", 76) -> "=== User"
    value == User("Joe", 76) -> "== User(Joe, 76)"
    value is User -> "is User"
    else -> "SomeValue"
}
```

### 코틀린 패턴 매칭의 제약

코틀린은 리스와 같은 매개변수를 포함하는 타입이나 함수의 타입에 대한 패턴 매칭을 지원하지 않는다.

```kotlin
fun sum(numbers: List<Int>): Int = when {
    numbers.isEmpty() -> 0
    else -> numbers.first() + sum(numbers.drop(1))
}
```

## 2.7 객체 분해

객체 분해란 어떤 객체를 구성하는 프로퍼티를 분해하여 편리하게 변수에 할당하는 것을 말한다.

```kotlin
data class User(val name: String, val age: Int)

fun main() {
    val user: User = User("kotlin", 28)

    val (name, age) = user

    println("name: $name") // "name: kotlin" 출력
    println("age: $age") // "age: 28" 출력
}
```

만약 특정 변수에 대해서 무시하고 싶다면 "_"를 사용하면 된다.

```kotlin
val (name, _) = User("name", 10)
```

## 2.8 컬렉션

함수형 프로그래밍에서는 기본적으로 불변 자료구조를 사용한다. 코틀린에서는 불변과 가변 자료구조를 분리해서 제공한다.

- 불변 자료구조 : List, Set, Map
- 가변 자료구조 : MutableList, MutableSet, MutableMap

### 리스트와 세트

불변 자료구조인 리스트는 **add** 를 할 수 없기 때문에 **plus** 를 통해서 새 list를 반환한다.

```kotlin
val list = listOf(1, 2, 3, 4, 5)
val newList = list.plus(6)

println(list) // 1, 2, 3, 4, 5
println(newList) // 1, 2, 3, 4, 5, 6
```

가변 자료구조의 예시는 다음과 같다.

```kotlin
val list: MutableList<Int> = mutableListOf(1, 2, 3, 4, 5)
list.add(6)

println(list) // 1, 2, 3, 4, 5, 6
```

### 맵

코틀린에서는 키와 값으로 된 **Pair** 라는 자료구조를 제공해준다. 맵은 이 Pair를 여러 개 가진 자료구조다.

```kotlin
val map1 = mapOf(1 to "One", 2 to "Two")
val map2 = map1.plus(Pair(3, "Three"))

println(map1) // "{1=One, 2=Two}" 출력
println(map2) // "{1=One, 2=Two, 3=Three}" 출력

val mutableMap = mutableMapOf(1 to "One", 2 to "Two")
mutableMap.put(3, "Three")

println(mutableMap) // "{1=One, 2=Two, 3=Three}" 출력

mutableMap.clear()

println(mutableMap) // "{}" 출력
```

## 2.9 제네릭

제네릭(generic)은 객체 내부에서 사용할 데이터 타입을 외부에서 정하는 기법이다.

클래스의 변수 타입이 다음과 같이 한가지로 고정이 되어 있으면 value 라는 변수에는 Int 타입의 값만 넣을 수 있다.

```kotlin
class Box(t: Int) {
    var value = t
}
```

아직 타입이 결정되지 않은 변수를 가지게 하는 코드는 다음과 같이 작성하면 된다.

```kotlin
class Box<T>(t: T) {
    var value = t
}
```

그러면 Box를 통해서 객체를 생성할 때 타입이 결정된다.

```kotlin
val box = Box("kotlin")
```

### 제네릭 함수 선언

다음과 같이 리스트의 첫 번째 값을 꺼내오는 함수라면 타입에 관계없이 동작하므로 제네릭을 활용해 일반화하기에 적합하다.

```kotlin
fun <T> head(list: List<T>): T {
    if (list.isEmpty()) {
        throw NoSuchElementException()
    }
    return list[0]
}
```

## 2.10 코틀린 표준 라이브러리

간결하고 명료한 코드를 작성하는데 도움을 주는 확장 함수들을 알아본다.

### let 함수

let 함수는 다음과 같이 선언되어 있다.

```kotlin
fun <T, R> T.let(block: (T) -> R): R
```

객체를 받아서 다른 객체로 변환 시키고 그것을 반환한다.

```kotlin
val person = Person("FP", 30)
person.name = "kotlin"
person.age = 10
println("$person") // "Person(name=Kotln, age=10)" 출력

val person = Person("FP", 30)
val result = person.let {
    it.name = "Kotlin"
    it.age = 10
    it
}
```

let 함수는 널 처리에도 유용하게 사용된다.

```kotlin
data class User(val firstName: String, val lastName: String)

fun printUserName(user: User?) {
    if (user != null) {
        println(user.firstName)
    }
}

fun printUserName(user: User?) {
    user?.let { println(it.firstName) }
}

val user: User? = User("Joe", "Cho")
val name = user?.let { it.lastName + it.firstName } ?: "lazysoul"
```

### with 함수

with 함수는 다음과 같이 선언되어 있다.

```kotlin
fun <T, R> with(receiver: T, block: T.() -> R): R
```

**T.()** 는 람다 리시버라고 부른다. 람다 리시버가 있는 경우에는 receiver로 받은 객체의 프로퍼티를 this를 사용하지 않고 접근할 수 있다. 다만 객체 자체를 접근할 때는 this를 사용한다.

```kotlin
val person = Person("FP", 30)
val result = with(person) {
    name = "Kotlin"
    age = 10
    this
}
println(result) // "Person(name=Kotlin, age=10)" 출력
```

### run 함수

run 함수의 첫 번째 선언

```kotlin
fun <T, R> T.run(block: T.() -> R): R
```

let 함수와 비교하면 리시버가 없기 때문에 block 안에 it를 사용하지 않아도 되고 
with 함수와 비교하면 리시버의 확장 함수 처럼 사용한다.

```kotlin
val person = Person("FP, 30)
val result = person.run {
    name = "Kotlin"
    age = 10
    this
}

println(result) // "Person(name=Kotlin, age=10)" 출력
```

this를 사용하고 싶지 않으면 다음과 같이 사용할 수 있다.

```kotlin
val person = run {
    val name = "kotlin"
    val age = 10
    Person(name, age)
}

println(person) // "Person(name=Kotlin, age=10)" 출력
```

### apply 함수

apply 함수 선언

```kotlin
fun <T> T.apply(block: T.() -> Unit): T
```

apply 함수는 run 함수와 달리 block 함수 내에 객체 자체를 변환한다.

```kotlin
val person = Person("FP", 30)
val result = person.apply {
    name = "Kotlin"
    age = 10
}

println(result) // "Person(name=Kotlin, age=10)" 출력
```

반환 값을 명시하지 않아도 되기 때문에 let, with, run 함수보다 사용하기 편하다.

### also 함수

also 함수 선언

```kotlin
fun <T>.also(block: (T) -> Unit): T
```

also는 람다 리시버가 없기 때문에 it을 통해서 객체를 it을 통해서 접근한다.

```kotlin
val person = Person("FP", 30)
val result = person.also {
    it.name = "Kotlin"
    it.age = 10
}

println(result) // "Person(name=Kotlin, age=10)" 출력
```

> apply와 also 함수는 빌더 패턴과 동일한 용도로 사용한다.

### let, with, run, apply, also 함수 비교

||let|run|with|apply|also|
|:---:|:---:|:---:|:---:|:---:|:--:|
|코드 블록|람다식|람다 리시버|람다 리시버|람다 리시버|람다식|
|접근|it|this|this|this|it|
|반환값|람다식 반환값|람다식 반환값|람다식 반환값|자기 자신|자기 자신|

### use 함수

클로저블 객체를 자원을 사용한 후 클로즈 해준다. 자바의 try-with-resource와 같은 기능을 한다.

```kotlin
val property = Properties()
FileInputStream("config.properties").use {
    property.load(it)
} // FileInputStream이 자동으로 close됨
```

use 함수의 블록 내 동작이 완료되면 property는 자동으로 클로즈되어 자원이 정리된다.

## 2.11 변성

타입의 계층 관계에서 타입의 가변성을 처리한 방식이다. 

- 타입 S가 T의 하위 타입일 때
    - Box[S]와 Box[T]는 상속 관계가 없다. -> 무공변
    - Box[S]는 Box[T]의 하위 타입이다. -> 공변
    - Box[T]는 Box[S]의 하위 타입이다. -> 반공변

### 무공변의 의미와 예

다음 예제를 보자.

```kotlin
interface Box<T>
open class Language
open class JVM
class Kotlin: JVM()

val languageBox = object: Box<Language> {}
val jvmBox = object: Box<JVM> {}
val kotlinBox = object: Box<Kotlin> {}

fun main() {
    invariant(languageBox) // 컴파일 오류
    invariant(jvmBox)
    invariant(kotlinBox) // 컴파일 오류
}

fun invariant(value: Box<JVM>) {}
```

제네릭에 아무 것도 넣지 않기 때문에 상위, 하위 타입 관계없이 컴파일 오류가 발생한다.

### 공변의 의미와 예

다음 예제를 보자

```kotlin
interface Box<T>
open class Language
open class JVM
class Kotlin: JVM()

val languageBox = object: Box<Language> {}
val jvmBox = object: Box<JVM> {}
val kotlinBox = object: Box<Kotlin> {}

fun main() {
    covariant(languageBox) // 컴파일 오류
    covariant(jvmBox)
    covariant(kotlinBox)
}

fun covariant(value: Box<out JVM>) {}
```

**out** 이라는 키워드를 넣어줬기 때문에 **공변** 하위 타입에 대해서 허용한다.

> Java의 extend 키워드와 비슷하다.

### 반공변의 의미와 예

다음 예제를 보자.

```kotlin
interface Box<T>
open class Language
open class JVM
class Kotlin: JVM()

val languageBox = object: Box<Language> {}
val jvmBox = object: Box<JVM> {}
val kotlinBox = object: Box<Kotlin> {}

fun main() {
    contravariant(languageBox) 
    contravariant(jvmBox)
    contravariant(kotlinBox) // 컴파일 오류
}

fun contravariant(value: Box<in JVM>) {}
```

### in, out으로 변성 선언하기

