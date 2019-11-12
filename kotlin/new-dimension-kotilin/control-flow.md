# Control Flow

## 1. if else 문

```kotlin
var max = a
if (a < b) max = b
```

```kotlin
var max: Int
if (a > b) {
    max = a
} else {
    max = b
}
```

- Java의 if else 문과 동일하게 사용할 수 있다.

<br>

```kotlin
val max = if (a > b) a else b
```

- if 문이 식으로 사용되는 경우 값을 반환한다.
- if 문이 식으로 사용할 경우 반드시 else를 동반해야한다.

```kotlin
val max = if (a > b) {
    print("Choose a")
    a
} else {
    print("Choose b")
    b
}
```

- if 문이 식으로 사용되는 경우 branches들이 블록을 가질 수 있다.
- 블록의 마지막 구문이 반환 값으로 결정된다. (return을 안써도 된다는 의미)

```java
// java
int max = (a > b) ? a : b;
```

```kotlin
// kotlin
val max = if (a > b) a else b
```

- kotlin에는 삼항연산자가 없다. 그 이유는 if else문 자체가 삼항연산자의 역할을 잘하기 때문이다.

## 2. when

```kotlin
when(x) {
    1 -> print("x == 1")
    2 -> print("x == 2")
    else -> {
        print("x is neither 1 nor 2")
    }
}
```

- when문은 C계열 언어의 switch문을 대체한다.
- when문은 각각의 branches의 조건문이 만족할 때까지 위에서부터 순차적으로 인자를 비교한다. break문이 없어도 자동으로 break문이 적용된다.

```kotlin
var res = when(x) {
    100 -> "A"
    90 -> "B"
    80 -> "C"
    else -> "F"
}
```

- when문이 식으로 사용된 경우 조건을 만족하는 branch의 값이 전체 식의 결과 값이 된다.
- when문이 식으로 사용된 경우 else 문이 필수이다.

```kotlin
var res = when(x) {
    true -> "맞다"
    false -> "틀리다"
}
```

- when문이 식으로 사용된 경우 컴파일러가 else 문이 없어도 된다는 것을 입증할 수 있는 경우에는 else문이 생략가능하다.

```kotlin
when (x) {
    0,1 -> print("x == 0 or x == 1")
    else -> print("otherwise")
}
```

- 여러 조건들이 같은 방식으로 처리될 수 있는 경우, branch의 조건문에 콤마(,)를 사용하여 표기하면 된다.

```kotlin
when(x) {
    parseInt(x) -> print("s encodes x")
    1 + 3 -> print("4")
    else -> print("s does not encode x")
}
```

- Branch의 조건문에 함수나 식을 사용할 수 있다.

```kotlin
val validNumbers = listOf(3, 6, 9)
when(x) {
    in validNumbers -> print("x is valid")
    in 1..10 -> print("x is in the range")
    !in 10..20 -> print("x is outside the range")
    else -> print("none of the above")
}
```

- range나 collection에 in이나 !in으로 범위 등을 검사할 수 있다.

```kotlin
fun hasPrefix(x: Any) = when(x) {
    is String -> x.startsWith("prefix")
    else -> false
}
```

- is나 !is를 이용하여 타입도 검사할 수 있다. 이 때 스마트 캐스트가 적용된다.

```kotlin
when {
    x.isOdd() -> print("x is odd")
    x.isEven() -> print("x is even")
    else -> print("x is funny")
}
```

- when은 if-else if 체인을 대체할 수 있다.
- when에 인자를 입력하지 않으면 논리연산으로 처리된다.

## 3. For Loops

```kotlin
for (item in collection)
  print(item)
```

- for문은 iterator을 제공하는 모든 것을 반복할 수 있다. (반대로 말하면 반드시 iterator을 제공하는 것만 반복할 수 있다.)

```kotlin
for (item in collection) {
    print(item.id)
    print(item.name)
}
```

- for문의 Body에 블록이 올 수 있다.

```kotlin
fun main(args: Array<String>) {
    val myData = MyData()
    for (item in myData) {
        print(item)
    }
}

class MyIterator {
    val data = listOf(1, 2, 3, 4, 5)
    var idx = 0
    operator fun hasNext() : Boolean {
        return data.size > idx
    }

    operator fun next(): Int {
        return data[idx++]
    }
}

class MyData {
    operator fun iterator(): MyIterator {
        return MyIterator()
    }
}
```

- For문을 지원하는 iterator의 조건은 다음과 같다.
  - iterator()를 반환하는 것이 있어야한다.
  - 그 iterator() 메소드를 통해 반환되는 객체에는 **next()**와 **hasNext(): Boolean** 메소드가 존재해야한다.
  - **iterator()**, **next()**, **hasNext()**는 **operator**로 표기되어야 한다.

<br>

```kotlin
val array = arrayOf("가", "나", "다")
for (i in arrya.idices) {
    println("$i: ${array[i]}")
}
```

- for문을 사용할 때, index를 이용하고 싶다면 **idices** 키워드를 사용하면된다.

```kotlin
val array = arrayOf("가", "나", "다")
for((index, value) in array.withIndex()) {
    println("$index: ${value}")
}
```

- Index를 이용하고 싶을 때, withIndex()를 사용할 수 있다.

<br>

## 4. while loops

```kotlin
while (x > 0) {
    x--
}

do {
    val y = retrieveData()
} while (y != null)
```

- while, do-while문은 Java와 거의 같다. 
- do-while문에서 body의 지역변수를 do-while문의 조건문이 참조할 수 있다.