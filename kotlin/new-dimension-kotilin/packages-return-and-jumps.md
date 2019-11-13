# Packages, Return and Jumps

## 1. Package

```kotlin
package foo.bar

fun baz() {}

class Goo {}

fun maiun(args: Array<String>) {
    foo.bar.baz()
    foo.bar.Goo()
}
```

- 소스 파일은 패키지 선언으로 시작된다.
- 모든 콘텐츠(클래스, 함수, ...)패키지에 포함된다.
- 패키지를 명세하지 않으면 이름이 없는 기본 패키지에 포함된다.

<br>

## 2. imports

```kotlin
// Bar 1개만 import함
import foo.Bar

// 'foo' 패키지에 모든 것을 import함
import foo.*

// foo.Bar
// bar.Bar 이름이 충돌 나는 경우 'as' 키워드로 로컬 리네임 가능
import bar.Bar as bBar
```

Java와 다른 점은 'as'를 통해서 로컬 리네임이 가능하다는 점이다.

<br>

## 3. return and Jumps

```kotlin
fun sum(a: Int, b: Int): Int {
    println("A: $a, b: $b")
    return a + b
}
```
- **return** : 함수나 익명 함수에서 반환

```kotlin
for (x in 1..10) {
    if (x > 2) {
        break
    }

    println("x: $x")
}
```
- **break** : 루프를 종료시킨다.

```kotlin
for (x in 1..10) {
    if (x > 2) {
        continue
    }
    println("x: $x")
}
```

<br>

## 4. label

```kotlin
loop@ for (i in 1..10) {
    println("--- i : $i ---")

    for (j in 1..10) {
        println("j: $j")
        if (i + j > 12) {
            break@loop
        }
    }
}
```

- 이중 반복문의 break를 label(예제에서 loop@)를 통해서 구현할 수 있다.

```kotlin
loop@ for (i in 1..10) {
    println("--- i: $i ---")

    for (j in 1..10) {
        if (j < 2) {
            continue@loop
        }
        println("j: $j")
    }
}
```

- 이중 반복문의 continue를 label(예제에서 loop@)를 통해서 구현할 수 있다.

```kotlin
fun foo() {
    var ints = lisOf(0, 1, 2, 3)

    ints.forEach(fun(value: Int) {
        if (value == 1) return
        print(value)
    })
    print("End")
}
```

<br>

## 5. 코틀린에서 중첩될 수 있는 요소들

- 함수 리터럴
- 지역함수
- 객체 표현식
- 함수

```kotlin
fun foo() {
    var ints = listOf(0, 1, 2, 3)

    ints.forEach(fun(value: Int) {
        if (value == 1) return
        print(value)
    })
    print("End")
}
```

```kotlin
fun foo() {
    var ints = listOf(0, 1, 2, 3)

    ints.forEach {
        if (it == 1) return
        print(it)
    }
    print("End")
}
```

- 첫 번째 예제는 중첩될 수 있는 함수이기 때문에 return 문이 foreach 문 안의 함수에만 적용이 된다. 

- 두 번째 예제의 경우 제일 가까운 함수가 foo()가 되기 때문에 it이 1일 때 foo() 함수가 멈춘다.

```kotlin
fun foo() {
    var ints = listOf(0, 1, 2, 3)
    ints.forEach label@ {
        if(it == 1) return@label
        print(it)
    }
    print("End")
}
```

- label을 통해서 끝지점을 지정해놓으면 람다식을 통해서 1인 경우만 출력이 안되도록 수정할 수 있다.


```kotlin
fun foo() {
    var ints = listOf(0, 1, 2, 3)
    ints.forEach {
        if(it == 1) return@forEach
        print(it)
    }
    print("End")
}
```

- 암시적으로 forEach가 Label로 선언이 되어 있기 때문에 간단하게 사용할 수 있다.

```kotlin
fun foo(): list<String> {
    var ints = listOf(0, 1, 2, 3)
    val resul = ints.map {
        if (it == 0) {
            return@map "zero"
        }
        "number $it"
    }
    return result
}
```

- return 값은 위와 같이 설정할 수 있다.

