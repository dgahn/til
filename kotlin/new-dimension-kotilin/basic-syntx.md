# Basic Syntax

## 1. 패키지의 정의

```kotlin
package io.github.dgahn.kotlinbasic

import java.util.*
```

- Kotlin의 패키지 선언은 Java와 마찬가지로 파일의 상단에 정의한다.

- Java와 다른 점은 패키지와 디렉터리를 일치하지 않아도 된다.

<br>

## 2. 함수 정의

```kotlin
fun sum(a:Int, b: Int): Int {
    return a + b;
}
```

- 함수를 정의하는 키워드는 **fun**이다.
- 리턴 타입이 함수명 뒤에 붙는다.
- 함수 몸체가 식(Expression)인 경우 return이 생략가능하다. Kotlin에서 return type 추론이 가능하기 때문이다.

```kotlin
fun printKotlin(): Unit {
    println("hello Kotlin");
}
```

- 리턴할 값이 없는 경우 Unit(Object)으로 리턴한다.
- **Unit**은 Java에서 void 리턴을 의미한다. 

```kotlin
fun printKotlin() {
    println("hello Kotlin");
}
```

- **Unit**은 생략이 가능하다.

<br>

## 3.지역 변수 정의

```kotlin
val a: Int = 1 // 즉시 할당
val b = 2 // "Int" type 추론
val c: int // 컴파일 오류, 초기화를 하지 않으면 컴파일에 오류가 발생한다.
c = 3 // 컴파일 오류, 읽기 전용이므로 추후에 값을 할당할 수 없다.
```

- **val** : 읽기 전용 변수 선언 키워드이다. Java의 **final**과 유사하다.

```kotlin
var x = 5
x += 1
```

- **var** : Mutable 변수

<br>

## 4. 주석

```kotlin
// 한줄 주석

/* 여러줄 
   주석 */

/* block comment가
   /* 중첩도 가능 */
*/
```

<br>

## 5. 문자열 템플릿

```kotlin
var a = 1
val s1 = "a is $a"

a = 2

val s2 = "${s1.replace("is", "was")}, but now is $a"
```

- String Interpolation(문자열 보간법)이 가능하다. 변수를 문자열에 안에 넣는 것을 의미한다.

<br>

## 6. 조건문

```kotlin
fun maxOf(a: Int, b: Int): Int {
    if (a > b) {
        return a
    } else {
        return b
    }
}
```

- 다음과 같이 조건식으로 사용이 가능하다.

```kotlin
fun maxOf(a: Int, b:Int) = if (a > b) a else b
```

- **nullable 마크**를 통해서 null일 수 있는 경우 컴파일러에 명시할 수 있다. 만약 **nullable 마크**를 명시하지 않으면 컴파일 오류가 발생한다.

```kotlin
fun printProduct(arg1:String, arg2: String) {
    val x: Int? = parseInt(arg1);
    val y: Int? = parseInt(arg2);

    if (x != null && y != null) {
        println(x * y)
    } else {
        print("either '$arg1' or '$arg2' is not a number")
    }
}
```

<br>

## 7. 자동 타입 변환

```kotlin
fun getStringLength(object: Any): Int? {
    if (obj is String) { // obj가 String이라면 자동으로 String 타입으로 변환됨
        return obj.length
    }

    return null
}
```

- 타입 체크만 해도 자동으로 타입 변환이 된다.
- when 문에서는 자동으로 타입이 변환되지는 않는다.

<br>

## 8. while loop

```kotlin
val items = listOf("apple", "banana", "kiwi")
var index = 0
while (index < items.size) {
    println("item at $index is ${items[index]}")
    index++
}
```

- **while**문은 Java와 다르지 않다.

<br>

## 9. when Expression

```kotlin
fun describe(obj: Any): String = 
  when (obj) {
      1 - > "One"
      "Hello" -> "Greeting"
      is Long -> "Long"
      !is String -> "Not a string"
      else -> "Unknown"
  }
```

- Java에서의 switch와 비슷하지만 전혀 다르다. 이유는 switch은 타입의 값만 체크하는데 **when**은 조건을 체크하기 때문이다.

<br>

## 10. ranges

```kotlin
for (x in 1..5) {
    print(x)
}
```

- **for**문을 위와 같이 축약해서 사용할 수 있다.

```kotlin
val x = 3
if (x in 1..10) {
    println("fits in range")
}
```

- **In** 연산자를 이용하면 **if**문을 통해서 숫자 범위를 체크할 수도 있다.

<br>

## 11. collections

```kotlin
val items = listOf("apple", "banana", "kiwi")
for (item in items) {
    println(item)
}
```

- 반복문과 **in**을 같이 쓰면 **향상된 for문**처럼 사용할 수 있다.

```kotlin
val items = setOf("apple", "banana", "kiwi")
when {
    "orange" in items -> println("juicy")
    "apple" in items -> println("apple is fine too")
}
```

- 조건문과 **in**을 같이 쓰면 컬렉션 중 해당 Object를 찾을 수 있다.

```kotlin
val fruits = listOf("banana", "avocado", "apple", "kiwi")
fruits.filter {it.startsWith("a")}
      .sortedBy {it}
      .map {it.toUpperCase()}
      .forEach{println(it)}
```

- 람다식을 이용해서 컬렉션에 filter, map 등의 연산이 가능하다.