# Basic types

**Kotlin**은 Java와 다르게 모든 타입이 객체이다. 모든 타입의 변수에서 멤버 메소드나 프로퍼티를 호출이 가능하다.

## 1. 숫자(Numbers)

- **Java**의 숫자형과 거의 비슷하게 처리하는데 **Kotlin**에서 Number는 클래스이기 때문에 **Java**의 **primitive Type**에 직접 접근할 수 없다.

- **Java**에서 숫자형이던 **char**가 **Kotlin**에서는 숫자형이 아니다.

<br>

## 2. 리터럴 (Literal)

- **10진수** : 123 (Int, Short)
- **Long** : 123L
- **Double** : 123.5 (Int, Short)
- **Float** : 123.5f
- **2진수** : 0b00001011
- **8진수** : 미지원 (Java: int I = 017;)
- **16진수** : 0X0F

<br>

## 3. Underscores in numberic literals (since 1.1)

```kotlin
val oneMilion = 1_000_000
val creditCardNumber = 1234_5678_9012_3456L
val socialSecurityNumber = 999_99_9999L
val hexBytes = 0xFF_EC_DE_5E
val bytes = 0b11010010_01101001_10010100_10010010
```

언더스코어를 통해서 수치를 표현할 수 있다.

<br>

## 4. Representation

```kotlin
// JVM primitive
val a: Int = 100
val b: Int = 100
print(a === b) // print 'true', "==="은 idendtity를 비교한다.

// Boxed
val boxedA: Int? = a
val anotherBoxedA: Int? = a
print(a == boxedA) // print 'true'
print(a === boxedA) // print 'false'
print(boxedA === anotherBoxedA) // print 'true'
```

Java 플랫폼에서 숫자형은 JVM primitive type으로 저장된다. 그래서 **val a: Int = 100**는 JVM에서 실제로 실행될 때, Java primitive Type으로 해석이 된다.
하지만 **Nullable** 마크나 **제네릭**을 사용하게 되면 박싱이되서 JVM 상에서 객체로 사용되어 identity가 유지되지 않는다.

<br>

## 5. Explicit Conversions

```kotlin
val a: Int = 1
val b: Long = a.toLong()

val c: Int = b.toInt()
```

- 작은 타입에서 큰 타입으로 변환할 때, 자동으로 변환되지 않기 때문에 변환 메소드를 사용 해야한다.

<br>

## 6. 문자(Characters)

```kotlin
fun check(c: Char) {
    if (c == 1) { /* ... */ } // ERROR
}

fun check(c: Char) {
    if (c == 'a') { /* ... */ } // OK
}

print('0'.toInt())
```

- **Kotlin**에서는 **Char**가 숫자로 취급되지 않는다.

<br>

## 7. 배열

```kotlin
var array: Array<String> = arrayOf("코틀린", "강좌")
println(array.get(0))
println(arrya[0])
println(array.size)
```

- 배열은 Array 클래스로 표현된다.
- get 또는 set이 [] 연산와 오버로딩 되어 있다.
- size 등의 유용한 멤버 함수를 포함하고 있다.

### 7.1 배열 생성

```kotlin
val b = Array(5, {i -> i.toString()})
val a = arrayOf("0", "1", "2", "3", "4")
```

- Array의 팩토리 함수를 이용해서 생성할 수 있다.
- arrayOf() 등의 라이브러리 함수를 이용해서 생성할 수 있다.

### 7.2 특별한 Array 클래스로

```kotlin
val x: IntArrya = intArrayOf(1, 2, 3)
x[0] = 7
println(x.get(0))
println(x[0])
println(x.size)
```

- Primitive 타입의 박싱 오버헤드를 없애기 위한 배열이다.
- IntArray, ShortArray
- Array를 상속한 클래스들은 아니지만, Array와 같은 메소드와 프로퍼티를 가진다.
- size 등 유용한 멤버 함수를 포함한다.

<br>

## 8. 문자열

```kotlin
var x: String = "Kotlin"
println(x.get(0))
println(x[0])
println(x.length)

for (c in x) {
    println(x)
}
```

- 자바의 문자열과 거의 비슷하다.
- 문자열은 String 클래스로 표현된다.
- String은 characters로 구성된다.
- s[i]와 같은 방식으로 접근이 가능하다. 하지만 immutable이라 변경이 불가능하다.

### 9.1 문자열 리터럴

```kotlin
val s = "Hello, world!\n"
```

- escaped string ("Kotlin")
  - 전통적인 방식으로 Java String과 거의 비슷하다.
  - Backslash를 사용하여 escaping을 처리한다.

```kotlin
val s = """'이것은 코틀린의 
|raw String
|입니다.'"""
```

- raw string ("""Kotlin""")
  - escaping 처리가 필요 없다
  - 개행이나 어떠한 문자를 포함할 수 있다.