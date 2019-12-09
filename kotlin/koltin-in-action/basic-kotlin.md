# 코틀린 기초


## 2.1 기본 요소: 함수와 변수

### 2.1.1 Hello, World!

코틀린을 통해 **Hello, world!" 콘솔을 출력하는 프로그램 예제는 다음과 같다.

```kotlin
fun main(args: Array<String>) {
  println("Hello, wrold!")
}
```

위의 코드를 통해 다음과 같은 특징을 알 수 있다.

- 함수를 선언할 때 **fun**이라는 키워드를 사용한다.
- 변수명 뒤에 변수 타임을 쓴다.
- 함수를 선언하는데 클래스 안에 선언할 필요가 없다.
- 배열도 일반적인 클래스다.
- 출력 함수는 **pritln()** 이다.
- 세미콜론을 붙이지 않아도 된다.

<br>

### 2.1.2 함수

반환 값이 있는 함수를 정의 해보자.

```kotlin
fun max(a: Int, b: Int): Int {
    return if (a > b) a else b
}
```

반환 값이 앞이 아니라 블록 전에 나온다.

#### 식이 본문인 함수

위의 코드를 더 간결하게 표현할 수 있다. 다음을 보자.

```kotlin
fun max(a: Int, b: Int): Int = if (a > b) a else b
```

위와 같이 등호와 식으로 이루어진 함수를 **식이 본문인 함수**라고 부른다. 여기서 반환 값을 생략할 수 있다.

```kotlin
fun max(a: Int, b: Int) = if (a > b) a else b
```

코틀린은 정적 타입 지정 언어다. 그리고 **식이 본문인 함수**는 
컴파일러가 함수 본문 식을 분석해서 식의 결과 타입을 함수 반환 타입으로 변경한다. 이런 기능을 **타입 추론**이라고 한다.

**식이 본문인 함수**만 반환 타입을 생략할 수 있도 **블록이 본문인 함수**는 반환 타입을 생략할 수 없다.

### 2.1.3 변수

코틑린에서는 변수를 선언할 때 타입을 명시하지 않아도 된다.

```kotlin
val answer = 42
```

만약에 원한다면 해도 된다.

```kotlin
val answer: Int = 42
```

이는 변수 또한 컴파일러가 초기확식을 분석하여 타입을 추론해주기 때문이다.
하지만 변수를 초기화하지 않는다면 반드시 변수 타입을 지정해야한다.

```kotlin
val answer: Int
answer = 42
```

#### 변경 가능한 변수와 변경 불가능한 변수

- **val** : 변경 불가능한 참조를 저장하는 변수다. 일단 초기화하면 재대입이 불가능하다. **value**의 줄임말이다.

- **var** : 변경 가능한 참조다. **variable**의 줄임말이다.

기본적으로 **val**로 선언하고 필요하면 **var**로 변경하자. **val**와 순수함수를 조합하여 함수형 프로그래밍에 가까워지도록 하자.

**val**는 반드시 한번만 초기화 되어야 한다. 조건에 따라 여러가지 값으로 초기화될 수 있다.

```kotlin
val message: String

if (canPerformOperation()) {
    message = "message"
} else {
    message = "Failed"
}
```

val 자체의 참조 값은 변경할 수 없지만 참조하는 내부 값은 변경할 수 있다.

```kotlin
val languages = arrayListOf("Java")
languages.add("kotlin")
```

**var** 키워드를 사용하면 변수의 값을 변경할 수 있지만 타입을 변경할 수는 없다.

```kotlin
var answer = 42
answer = "no answer" // 컴파일 오류
```

## 2.1.4 문자열 템플릿

다음 예제를 보자.

```kotlin
fun main(args: Array<String>) {
    val name = "kotlin"
    println("hello, $Kotlin")
}
```

문자열에서 변수를 사용할 수 있도록 문자열 템플릿이라는 기능을 제공한다.
복잡한 식인 경우에는 다음과 같이 사용하면 된다.

```kotlin
fun main(args: Array<String>) {
    println(${args[0]})
}
```

중괄호로 둘러싼 식 안에서 큰 따옴표도 사용할 수 있다.

```kotlin
fun main(args: Array<String>) {
    println("Hello, ${ if (args.size > 0) args[0] else "someone" })
}
```

<br>

## 2.2 클래스와 프로퍼티

코틀린을 통해서 일반적인 클래스를 선언하면 다음과 같다.

```kotlin
class Person(val name: String)
```

코틀린의 기본 접근 제한자는 **public**이기 때문에 public을 안써도 된다.

### 2.2.1 프로퍼티

자바에서는 데이터를 필드에 저장하고 멤버 필드의 가시성을 보통 비공개로 한다. 그리고 클래스가 필드를 접근할 수 있는 **접근자 메소드**를 제공한다.
자바에서 **필드**와 **접근자 메소드**를 묶어서 **프로퍼티**라고 부른다. 코틀린은 **프로퍼티**를 기본적으로 제공해준다. 즉 **val** or **var**로 변수를 선언해두면 기본적으로 **접근자 메소드**로 변수에 접근하는 것이다.


```kotlin
class Person(val name: String, var isMarried: Boolean)
```

- **val name: String** : 비공개 필드 name과 name을 읽을 수 있는 **getter**를 제공한다.
- **var isMarried: Boolean** : 비공개 필드 isMarried와 **getter**, **setter**를 제공한다.

**kotlin**에서 **getter**를 호출하는 방식은 객체에서 프로퍼티를 호출하면 된다.

```kotlin
println(person.name)
println(person.isMarried)
```

대부분의 프로퍼티에서는 그 프로퍼티의 값을 저장하기 위한 필드가 존재한다. 이를 **backing field**라고 부른다.

<br>

### 2.2.2 커스텀 접근자

```kotlin
class Rectangle(val height: Int, val width: Int) {
    val isSquare: Boolean
      get() { // 프로퍼티 게터 선언
          return height == width
      }
}
```

블록 안에 val로 프로퍼티를 선언하고 get를 위와 같이 선언할 수 있다. 위 **getter**의 경우 문을 식으로 바꾸면 다음과 같이 수 정할 수 있다.

```kotlin
class Rectangle(val height: Int, val width: Int) {
    val isSquare: Boolean
      get() = height == width
}
```

### 2.2.3 코틀린 소스코드 구조: 디렉터리와 패키지 

코틀린에서는 기본적으로 자바와 비슷하게 디렉터리와 패키지를 구성할 수 있다. 
다른 점은 디렉터리와 패키지가 동일할 필요 없다는 것이 있다. 
하지만 자바와 같이 디렉터리와 패키지를 동일하게 구성하는 것이 편리하다.

그리고 객체 뿐만 아니라 함수도 패키지를 통해서 **import** 할 수 있다. 
이는 자바와 다르게 함수가 꼭 클래스안에 들어갈 필요가 없기 때문이다.

## 2.3 선택 표현과 처리: enum과 when

when은 자바의 switch를 대치하는데 더 다양한 기능을 제공한다. when을 설명하면서 enum도 같이 공부한다.

### 2.3.1 enum 클래스 정의

색을 표현하는 enum을 다음과 같이 정의한다.

```kotlin
enum class Color {
    RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
}
```

코틀린에서 **enum**이라는 키워드는 소프트 키워드다. 이는 **enum**이 **class** 앞에 있을 때는 특별한 의미를 지니지만
다른 곳에서는 이름에 사용할 수 있기 때문이다. **enum**은 단순히 값을 열거하는 것 뿐만 아니라 프로퍼티나 메소드를 정의할 수 있다.

```kotlin
enum class Color (
    val r: Int, val g: Int, val b: Int
) {
    RED(255, 0, 0), ORANGE(255, 165, 0), YELLOW(255, 255, 0),
    GREEN(0, 255, 0), BLUE(0, 0, 255), INDIGO(75, 0, 150), VIOLET(238, 130, 238);

    fun rgb() = (r * 256 + g) * 256 + b
}

fun main(args: Array<String>) {
    println(Color.BLUE.rgb())
}
```

위 예제에서는 코틀린에서 유일하게 세미콜론을 사용하는 부분을 알 수 있다.

### 2.3.2 when으로 enum 클래스 다루기

```kotlin
fun getMnemonic(color: Color) {
    when (color) {
        Color.RED -> "Richard"
        Color.ORANGE -> "Of"
        Color.YELLOW -> "York"
        Color.GREEN -> "Gave"
        Color.BLUE -> "Battle"
        Color.INDIGO -> "In"
        Color.VIOLET -> "Vain"
    }
}

fun main(args: Array<String>) {
    println(getMnemonic(Color.BLUE))
}
```

**when**은 자바의 **switch**와 달리 **break**을 넣지 않아도 된다. 그리고 조건에 따라 따라 해당 분기만 실행한다.
조건이 여러가지 **or**인 경우 **,** 를 이용해서 사용할 수 있다.

```kotlin
fun getWarmth(color: Color) = when(color) = {
    Color.RED, Color.ORANGE, Color.YELLOW -> "warm"
    Color.GREEN -> "neutral"
    Color.BLUE, Color.INDIGO, Color.VIOLET -> "cold"
}

fun main(args: Array<String>) {
    println(getWarmth(Color.ORANGE))
}
```

### 2.3.3 when과 임의 객체를 함께 사용

```kotlin
fun mix(c1: Color, c2: Color) = when (setOf(c1, c2)) {
    when (setOf(c1, c2)) {
        setOf(RED, YELLOW) -> ORANGE
        setOf(YELLOW, BLUE) -> GREEN
        setOf(BLUE, VIOLET) -> INDIGO
        else -> throw Exception("Dirty color")
    }
}

fun main(args: Array<String>) {
    println(mix(BLUE, YELLOW))
}
```

**setOf**는 **Set**을 만들어주는 함수다. 순서에 상관 없게 만들어주므로 **BLUE, YELLOW**가 **Set**을 형성한다. 그리고
**when**은 동등성을 검사하기 때문에 **mix(BLUE, YELLOW)**를 호출했을 때 결과는 **GREEN**이 나온다.

### 2.3.4 인자 없는 when 사용

**mix** 함수의 불필요한 객체 생성을 위하여 다음과 같이 인자 없는 when을 사용할 수 있다.

```kotlin
fun mixOptimized(c1: Color, c2: Color) = 
    when {
        (c1 == RED && c2 == YELLOW) ||
        (c1 == YELLOW && c2 == RED) -> ORANGE
        (c1 == YELLOW && c2 == BLUE) ||
        (c1 == BLUE && c2 == YELLOW) -> GREEN
        (c1 == BLUE && c2 == VIOLET) ||
        (c1 == VIOLET && c2 == BLUE) -> INDIGO
        else -> throw Exception("Dirty color")
    }

fun main(args: Array<String>) {
    println(mixOptimized(BLUE, YELLOW))
}
```

**mix** 함수와 같은 일을 하지만 객체를 생성하지 않는다. 하지만 가독성이 떨어진다는 단점이 생긴다.

### 2.3.5 스마트 캐스트: 타입 검사와 타입 캐스트를 조합

인터페이스와 클래스를 다음과 같이 선언하였다.

```kotlin
interface Expr

class Num(val value: Int): Expr

class Sum(val value: Expr, val right: Expr): Expr
```

코틀린에서 타입 검사는 **is**라는 키워드를 이용해서 한다.

```kotlin
fun eval(e: Expr): Int {
    if (e is Num) { // 타입 검사를 하면 자동으로 캐스팅이 된다.
        val n = e as Num // 그러므로 불필요한 캐스팅이다.
        return n.value
    }
}
```

위와 같이 자동으로 캐스트하는 것은 **스마트 캐스트**라고 한다.

### 2.3.6 리팩토링: if를 when으로 변경

값을 만들어내는 if 식은 다음과 같다.

```kotlin
fun eval(e: Expr): Int = 
    if (e is Num) {
        e.value
    } else if (e is Sum) {
        eval(e.right) + eval(e.left)
    } else {
        throw IllegalArgumentException("Unknown expression")
    }

fun main(args: Array<String>) {
    println(eval(Sum(Num(1), Num(2))))
}
```

이를 when으로 변경하면 다음과 같다.

```kotlin
fun eval(e: Expr): Int {
    when (e) {
        is Num -> e.value
        is Sum -> eval(e.right) + eval(e.left)
        else -> throw IllegalArgumentException("Unknown expression")
    }
}
```

**when** 식을 동등성 검사가 아닌 다른 기능에도 사용할 수 있다. 

### 2.3.7 if와 when의 분기에서 블록 사용

if나 when의 모든 분기는 블록을 사용할 수 있다. 그 예제는 다음과 같다. 주의해야할 점은 블록의 마지막 식이 블록의 결과라는 점이다.

```kotlin
fun evalWithLoggin (e: Expr): Int =
    when (e) {
        is Num -> {
            println("num: ${e.value}")
            e.value
        }
        is Sum -> {
            val left = evalWithLogging(e.left)
            val right = evalWithLogging(e.right)
            println("sum: $left + $right")
            left + right
        }
        else -> throw IllegalArgumentException("Unknown expression")
    }
```

## 2.4 대상을 이터레이션: while과 for 루프

### 2.4.1 while 루프

코틀린에는 **while**과 **do-while** 루프가 있다. 자바와 차이가 없다.

## 2.4.2 수에 대한 이터레이션: 범위와 수열

코틀린에서는 초깃값, 증가 값, 최종 값을 이용한 **for** 루프는 존재하지 않는다. 대신 **범위**를 사용한다. 
범위는 **..** 을 이용하여 시작 값과 끝 값을 연결해서 범위를 만든다.

```kotlin
val oneToTen = 1..10
```

코틀린의 범위는 양끝을 모두 포함하는 구간이다. 어떤 범위에 속한 값을 일정한 순서로 이터레이션하는 경우를 **수열**이라고 한다.

수열을 통해 **피즈버즈** 게임을 구현해보자. 3으로 나눠 떨어지면 **Fizz** 5로 나눠 떨어지면 **Buzz** 그리고 3과 5로 모두 나눠 떨어지면 **FizzBuzz**를 외친다.

```kotlin
fun fizzBuzz(i: Int) = when {
    i % 15 == 0 -> "FizzBuzz"
    i % 3 == 0 -> "Fizz"
    i % 5 == 0 -> "Buzz"
    else -> "$i"
}

fun main(args: Array<String>) {
    for (i in 1..100) {
        println(fizzBuzz(i))
    }
}
```

100부터 1까지 세면서 짝수만으로 해보자.

```kotlin
fun main(args: Array<String>) {
    for (i in 100 downTo 1 step 2) {
        println(fizzBuzz(i))
    }
}
```

여기서 **100 downTo 1**은 100 부터 1까지 세도록 변경하고 **step 2**에 의해서 증가 값이 변경한다.

### 2.4.3 맵에 대한 이터레이션

**for** 루프를 사용해 이터레이션하려는 컬렉션의 원소를 푸는 방법은 다음과 같다.

```kotlin
val binaryReps = TreeMap<Char, String>()

for (c in 'A'..'F') {
    val binary = Integer.toBinaryString(c.toInt())
    binaryReps[c] = binary
}

for ((letter, binary) in binaryReps) {
    println("$letter = $binary")
}
```

자바에서 **binaryReps.put(c, binary)** 와 코틀린에서 **binaryReps[c] = binary**는 같다.

**arrayList**는 다음과 같이 이터레이션을 할 수 있다.

```koltin
val list = arrayListOf("10", "11", "1001")
for ((index, element) in list.withIndex()) {
    println("$index: $element")
}
```

### 2.4.4 in으로 컬렉션이나 범위의 원소 검사

**in** 연산자를 사용하면 어떤 범위에 속하는지 알 수 있고 **!in** 연산자를 사용하면 어떤 범위에 속하지 않는 것을 알 수 있다.

```kotlin
fun isLetter(c: Char) = c in 'a'..'z' || c in 'A'..'Z'
fun isNotDigit(c: Char) = c !in '0'..'9'

fun main(args: Array<String>) {
    println(isLetter(('q')))
    println(isNotDigit('x'))
}
```

**when**에서 **in** 사용자는 다음과 같다.

```kotlin
fun recognize(c: Char) = when (c) {
    in '0'..'9' -> "It's a digit!"
    in 'a'..'z', in 'A'..'Z' -> "It's a letter!"
    else -> "I don't know..." 
}

fun main(args: Array<String>) {
    println(recognize('8'))
}
```

**Comparable** 인터페이스를 구현한 클래스라면 모두 이터레이션할 수 있다. 하지만 **String** 클래스를 이터레이션을 할 수는 없다.
그런 경우 이 **in**이 매우 유용하다.

```kotlin
println("Kotlin" in "Java".."Scala")
```

## 2.5 코틀린의 예외 처리

코틀린의 예외처리는 자바나 다른 언어의 예외 처리와 비슷하다. 함수는 정상적으로 종료할 수 있지만 오류가 발생하면 예외를 **throw**할 수 있다.

```kotlin
if (percentage !in 0..100) {
    throw IllegalArgumentException("A percentage value must be between 0 and 100: $percentage")
}
```

### 2.5.1 try, catch, finally

자바와 마찬가지로 **try, catch, finally**을 사용할 수 있다.

```kotlin
fun readNumber(reader: BufferedReader): Int? {
    try {
        val line = reader.readLine()
        return Integer.parseInt(line)
    } catch (e: NumberFormatException) {
        return null
    } finally {
        reader.close()
    }
}

fun main(args: Array<String>) {
    println(readNumber(reader))
}
```

자바 코드와 다른 점은 **throws** 절이 코드에 없다는 점이다. 코틀린은 체크 예외와 언체크 예외를 구별하지 않기 때문이다. 이유는 프로그래머가 필요에 따라 예외를 잡아내게 하기 위함이다.

### 2.5.2 try를 식으로 사용

코틀린에서 **try**도 다른 **if**와 **when**과 마찬가지로 식으로 사용할 수 있다.

```kotlin
fun readNumber(reader: BufferedReader) {
    val number = try {
        Integer.parseInt(reader.readLine())
    } catch (e: NumberFormatException) {
        return
    }
    println(number)    
}
```

예외가 발생하면 다음과 같이 **null**을 반환하도록 할 수 있다.

```kotlin
fun readNumber(reader: BufferedReader) {
    val number = try {
        Integer.parseInt(reader.readLine())
    } catch (e: NumberFormatException) {
        null
    }
    println(number)    
}
```
