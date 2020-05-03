# 3. 재귀

함수형 프로그래밍에서는 반복문 대신 재귀를 사용한다. 

## 3.1 함수형 프로그래밍에서 재귀가 가지는 의미

재귀는 함수 내부에서 자기 자신을 호출하는 함수를 정의하는 방법을 말한다. 

### 피보나치 수열을 명령형 프로그래밍으로 구현한 예제

동적 계획법(복잡한 프로그램을 간단한 문제로 나누어서 접근하는 방식)을 통해서 피보나치 수열을 구현 해보자.

```kotlin
fun main() {
    println(fiboDynamic(10, IntAray(100))) // "55" 출력
}

private fun fiboDynamic(n: Int, fibo: IntArray): Int {
    fibo[0] = 0
    fibo[1] = 1

    for (i in 2..n) {
        fibo[i] = fibo[i - 1] + fibo[i - 2]
    }

    return fibo[n]
}
```


**fiboDynamic()** 함수는 한번에 수열을 구하지 않았고 단계별로 피보나치 수열을 구했다. 위 예제의 경우 피보나치 수열을 담을 배열의 크기를 미리 제한했기 때문에
100까지만 계산할 수 있다.

### 피보나치 수열을 재귀로 구현한 예제

```kotlin
fun main {
    println(fiboRecursion(10))
}

private fun fiboRecursion(n: Int): Int = when(n) {
    0 -> 0
    1 -> 1
    else -> fiboRecursion(n - 1) + fiboRecursion(n - 2)
}
```

메모리에 값을 저장하지 않고 스택에 값을 저장해둔다.

### 함수형 프로그래밍에서 재귀 

함수형 프로그래밍에서는 어떻게(how) 값을 계산할 수 있을지 선언하는 대신 무엇(what)을 선언할지 고민해야한다.

반복문은 구조적으로 어떻게 동작해야 하는지를 명령하는 구문이다. 그렇기 때문에 반복문을 재귀로 풀어야 한다. 

다만, 재귀는 다음과 같은 단점을 가지고 있다.

- 동적 계획법 방식에 비해서 성능이 느리다.

- 스택 오버플로 오류(stack overflow error)가 발생할 수 있다.

이 문제를 해결하는 방법은 앞으로 예제를 통해서 소개할 것이다.

## 3.2 재귀를 설계하는 방법

Hello를 출력하는 프로그램을 재귀로 작성해보자.

```kotlin
private fun helloFunc(n: Int) {
    println("Hello")
    helloFunc()
}
```

위와 같이 작성하면 종료 조건이 없기 때문에 끝도 없이 **hello**가 출력될 것이다.

```kotlin
private fun helloFunc(n: Int) {
    when {
        n < 0 -> return
        else -> {
            println("Hello")
            helloFunc(n - 1)
        }
    }
}
```

재귀를 반복할 때마다 n이 1씩 감소하면서 n이 0보다 작아지면 프로그램은 종료된다.

### 재귀 함수 설계 방법

재귀에서 빠져 나오기 위해서는 종료조건이 한 개 이상 존재해야한다.

재귀 함수를 정의하기 위해서 다음과 같은 접근 방식을 사용하자.

- 종료조건 정의
- 함수의 입력을 분할하여 어떤 부분에서 재귀 호출을 할지 결정
- 함수의 입력 값이 종료조건으로 수렴하도록 재귀 호출의 입력값을 결정

종료조건을 정의할 때는 **자료구조가 더는 쪼개지지 않아 재귀의 과정이 더 이상 의미 없는 값**을 사용한다. 이 값을 **항등값**이라고 한다.

### 재귀가 수행되는 흐름 관찰해 보기

```kotlin
fun main() {
    println(func(5))
}

fun func(n: Int) = when {
    n < 0 -> 0
    else -> n + func(n - 1)
}
```

x의 n승을 구하는 함수를 재귀로 구현해보자.

```kotlin
fun power(x: Double, n: Int): Double = when {
    n < 2 -> x
    else -> x * power(x, n - 1)
}
```

### 재귀 함수 설계 방법을 사용하여 코드를 구현하기

maxium 함수를 만들어보자. 먼저 종료 조건을 생각해보자.

- 입력 리스트가 비어 있을 경우, 최댓값을 구할 수 없기 때문에 오류가 발생한 후 종료될 것이다.

- 입력 리스트에 구성요소가 한 개인 경우, 그 값이 최댓값이므로 종료될 것이다.

- 입력 리스트에 구성요소가 여러 개인 경우, 리스트의 첫 번째 값이 나머지 값들의 최댓값보다 크다면 첫 번째 값이 최댓값이다. 나머지 값들의 최댓값이 첫 번째 값보다 크다면 나머지 값들의 최댓값이 입력 리스트의 최댓값이다.

```kotlin
fun List<Int>.head() = first()
fun List<Int>.tail() = drop(1)

fun main() {
    println(maximum(listOf(1, 3, 2, 8, 4))) 
}

fun maximum(items: List<Int>): Int = when {
    items.isEmpty() -> error("empty list")
    1 == items.size -> items[0]
    else -> {
        val head = items.head()
        val tail = items.tail()
        val maxVal = maximum(tail)
        if (head > maxVal) head else maxVal
    }
}
```

### reverse 함수 예제

입력으로 들어온 리스트의 값들을 뒤집는 재귀 함수를 작성하자.

```kotlin
fun String.head() = first()
fun String.tail() = drop(1)

fun main() {
    println(reverse("abcd")) // "dcba"
}

fun reverse(str: String): String = when {
    str.isEmpty() -> ""
    else -> reverse(str.tail()) + str.head()
}
```

종료조건은 항등값이 무엇인지 생각해야한다. **""** 은 뒤집어도 **""** 이기 때문에 문자열을 뒤집는 것에서는 항등값이 된다.
그렇기 때문에 종료조건에 **""**를 넣어줬다. 그리고 재귀함수가 호출될 때마다 첫번째 요소를 뺀 나머지를 가지고 reverse를 호출하고 **head**를 출력하기 때문에
재귀 호출이 완료되면 뒤집힌 문자열을 반환한다.

### 연습문제

> 10진수 숫자를 입력 받아서 2진수 문자열로 변환하는 함수를 작성하라.

```kotlin
fun toBinary(n: Int): String = when {
    n == 0 -> ""
    else ->  toBinary(n / 2) + n % 2 
}
```

> 숫자를 두 개 입력 받은 후 두 번째 숫자를 첫 번째 숫자만큼 가지고 있는 리스트를 반환하는 함수를 만들어 보자. 예를 들어, replicate(3, 5)를 입력하면 5가 3개 있는 리스트 [5, 5, 5]를 반환한다.

```kotlin
fun replicate(x: Int, n: Int): List<Int> = when (n) {
    0 -> listOf()
    else -> listOf(x) + replicate(x, n-1)
}
```

### take 함수 예제

입력 리스트에서 입력받은 숫자만큼의 값만 꺼내오는 take 함수를 만들어 보자.

```kotlin
fun String.head() = first()
fun String.tail() = drop(1)

fun main() {
   println(take(3, listOf(1, 2, 3, 4, 5)))
}

fun take(n: Int, list: List<Int>): List<Int> = when {
    n <= 0 -> listOf()
    list.isEmpty() -> listOf()
    else -> listOf(list.head()) + take(n - 1, list.tail())
}
```

종료 조건은 입력 매개변수에 대해 따로따로 생각해봐야한다. n이 0이거나 리스트가 빈 리스트이면 가져올 것이 없기 때문에 항등값이 된다.

그리고 입력 값이 종료 조건에 수렴하도록 생각하면 된다.

### 연습문제

> 입력값 n이 리스트에 존재하는지 확인하는 함수를 재귀로 작성해보자.

```kotlin
fun find(n: Int, list: List<Int>): Boolean = when {
    list.isEmpty() -> false
    list.first() == n -> true
    else -> find(n, list.drop(1))
} 
```

### repeat 함수 예제

순수한 함수형 언어에서는 무한 자료구조를 지원하기 때문에 종료 조건이 없으면 무한하게 동작하거나 무한한 자료구조를 만든다. 코틀린은 순수한 함수형 언어는 아니지만, 시퀀스를 활용하여 무한 자료구조를 만들 수 있다.

숫자를 입력 받아서 무한히 반복 호출하는 repeat 함수를 만들어보자.

```kotlin
fun repeat(n: Int) Sequence<Int> = generateSequence(n) { it }
```

시퀀스를 이용해 구현하는 것이 좀 더 간결하고 좋지만 재귀를 이용해서 구현해보자.

```kotlin
fun main() {
    println(takeSequence(5, repeat(5))) // StackOverflowError 발생
}

fun repeat(n: Int): Sequence<Int> = sequenceOf(n) + repeat(n)
```

위를 실행하면 **+ 연산자**가 게으른 연산을 지원하지 않기 때문에 스택 오버 플로우가 발생한다.

**+ 연산자**가 게으른 연산을 지원하게 하기 위해서 연산자를 재정의 해보자.

```kotlin
operator fun <T> Sequence<T>.plus(other: () -> Sequence<T>) = object : Sequence<T> {
    private val thisIterator: Iterator<T> by lazy { this@plus.iterator() }
    private val otherIteraotr: Iterator<T> by lazy { other().iterator() }
    override fun iterator() = object : Iterator<T> {
        override fun next(): T =
            if(thisIterator.hasNext())
                thisIterator.next()
            else
                otherIterator.next()

        override fun hasNext(): Boolean = thisIterator.hasNext() || otherIterator.hasNext()
    }
}
```

이 코드를 이해하기 보다 시퀀스의 게으른 더하기 연산이 가능해졌다는 것을 알자.

```kotlin
fun main() {
    println(takeSequence(5, repeat(5))) // StackOverflowError 발생
}

fun repeat(n: Int): Sequence<Int> = sequenceOf(n) + repeat(n)
```

### 연습문제

> 코드 3-9의 take 함수를 참고하여 repeat 함수를 테스트하기 위해서 사용한 takeSequence 함수를 작성해 보자. 그리고 repeat 함수가 잘 동작하는지 테스트해 보자.  
> Hint : 함수의 선언 타입은 다음과 같다. 빈 시퀀스는 sequence.none()으로 표현한다.

```kotlin
fun takeSequence(n: Int, sequence: Sequence<Int>): List<Int> {
    TODO("모르겠음..")
}
```

### zip 함수 예제

두 개의 리스트를 받아서 하나의 리스트로 조합하는 zip 함수를 만들어보자.

```kotlin
fun main() {
    println(zip(listOf(1, 3, 5), listOf(2, 4))) // "[(1, 2), (3, 4)]" 출력
}

fun zip(list1: List<Int>, list2: List<Int>): List<Pair<Int, Int>> = when {
    list1.isEmpty() || list2.isEmpty() -> listOf()
    else -> listOf(Pair(list1.head(), list2.head())) + zip(list1.tail(), list2.tail())
}
```

### 연습문제

> 퀵정렬 알고리즘의 quicksort 함수를 작성해 보자.

```kotlin
fun quicksort() {
    TODO("ㅎ")
}
```

> 최대공약수를 구하는 gcd 함수를 작성해 보자.

```kotlin
fun gcd(m: Int, n: Int): Int = when {
    m < n -> gcd(n, m)
    m % n == 0 -> n
    else -> gcd(n, m % n)
}
```

## 3.4 메모이제이션으로 성능 개선하기

어떤 반복된 연산을 수행할 때 이전에 계산했던 값을 캐싱해서 중복된 연산을 제거하는 방법이다.

### 재귀적인 방식의 피보나치 수열 예제

피보나치 수열 예제를 다시보자.

```kotlin
fun fiboRecursion(n: Int): Int {
    println("fiboRecursion($n)")
    return when (n) {
        0 -> 0
        1 -> 1
        else -> fiboRecursion(n - 2) + fiboRecursion(n - 1)
    }
}
```

fiboRecursion(6)을 실행하면 총 24번이 호출된다.

### 메모이제이션을 사용한 피보나치 수열 예제

```kotlin
var memo = Array(100, { -1 })

fun fiboMemorization(n: Int): Int = when {
    n == 0 -> 0
    n == 1 -> 1
    memo[n] != -1 -> memo[n]
    else -> {
        println("fiboMemoization($n)")
        memo[n] = fiboMemoization(n - 2) + fiboMemoization(n - 1)
        memo[n]
    }
}
```

### 재귀의 문제점을 함수적으로 해결하기

순수한 함수는 부수효과가 없어야한다. 위와 같은 방법의 메모이제이션은 부수효과가 일어나므로 함수형 프로그래밍에 합당한 해법은 아니다.

캐싱을 위해서는 재귀 함수의 매개변수로 캐싱 값을 주면 된다.

```kotlin
fun fiboFP(n: Int): Int = fiboFP(n, 0, 1)

tailrec fun fiboFP(n: Int, first: Int, second: Int): Int = when (n) {
    0 -> first
    1 -> secode
    else -> fiboFP(n - 1, seconde, first + second)
}
```
첫번째 fiboFP 함수는 재귀 함수의 입력을 제한하기 위한 함수다. 실제로 재귀 호출을 수행하는 함수를 호출한다.

두번째 fiboFP 함수는 재귀 호출을 수행하는 함수로 이전에 계산된 값을 매개변수로 받는다.

## 3.5 꼬리 재귀로 최적화하기

**tailrec**은 언어 차원에서 제공하는 어노테이션이다. 만약 tailrec이 명시된 함수가 꼬리 재귀의 조건에 부합하지 않으면, IDE에서 경고 메시지를 준다.

### 꼬리 재귀 최적화란?

**꼬리 재귀란 어떤 함수가 직간접적으로 자기 자신을 호출하면서도 그 호출이 마지막 연산인 경우를 말한다.** 그리고 이 마지막 연산인 호출을 꼬리 호출(tail call)이라 한다.

1.0에서부터 수학의 코사인(cosine) 함수를 수행하여 더 이상 값이 변하지 않을 때까지 반복하는 재귀함수다.

```kotlin
tailrect fun findFixPoint(x: Double = 1.0): Double
  = if (x == Math.cos(x) x else findFixPoint(Math.cos(x)))
```

함수의 마지막 연산에서 재귀 호출이 이루어졌기 때문에 꼬리 재귀다. 그리고 **tailrec**을 명시하여 꼬리 재귀임을 컴파일러에게 알렸다.

### 연습문제

> factorial 함수가 꼬리 재귀인지 확인해보고 꼬리 재귀가 아니라면 최적화하자.

```kotlin
tailrec fun factorial(n: Int, m: Int): Int = when (n) {
    1 -> m
    else -> factorial(n - 1, n * m)
}

fun factorial(n: Int) = factorial(n - 1, n)
```

> power 함수가 꼬리 재귀인지 확인하고 꼬리 재귀가 아니라면 개선해보자.

```kotlin
tailrec fun power(x: Double, n: Int, result: Double): Double = when {
    n == 0 -> result
    else -> power(x, n - 1, result * x)
}

fun power(x: Double, n: Int) = power(x, n, 1.0)
```

### maximum 함수를 꼬리 재귀로 다시 작성하기

꼬리 재귀로 설계하는 방법도 일반 재귀를 설계하는 것과 크게 다르지 않다.

- 종료 조건을 정의한다.

- 입력을 분할하여 종료조건에 수렴하도록 재귀를 호출한다.

- 재귀 호출을 함수의 마지막 부분에서 수행해야 한다. 

- 내부 캐싱이 필요하면 중간 결과값을 재귀 함수의 매개변수를 통해서 전달한다.

```kotlin
fun List<Int>.head() = first()
fun List<Int>.tail() = drop(1)

fun maximum(items: List<Int>): Int = when {
    items.isEmpty() -> error("empty list")
    items.size == 1 -> {
        println("head : ${items[0]}, maxVal : ${items[0]}")
        items[0]
    }
    else -> {
        val head = items.head()
        val tail = items.tail()
        val maxValue = maximum(tail)
        println("head : $head, maxVal : $maxValue")
        if (head > maxValue) head else maxValue
    }
}

tailrec fun tailrecMaximum(items: List<Int>, acc: Int = Int.MIN_VALUE): Int = when {
    items.isEmpty() -> error("empty list")
    items.size == 1 -> {
        println("head: ${items[0]}, maxVal : $acc")
        acc
    } else -> {
        val head = items.head()
        val maxValue = if (head > acc) head else acc
        println("head : $head, maxVal : $maxValue")
        tailrecMaximum(items.tail(), maxValue)
    }
}
```

꼬리 재귀의 중간 값을 매개변수를 넘기면 일반 재귀보다 성능이 오른다.

중간 값을 매개변수를 넘기면 스택을 사용하지 않기 때문에 반복문과 동일한 호출한 과정을 보인다.

### reverse 함수를 꼬리 재귀로 다시 작성하기

```kotlin
fun reverse(str: String): String = when {
    str.isEmpty() -> ""
    else -> reverse(str.tail()) + str.head()
}

tailrec fun reverse(str: String, acc: String = ""): String = when {
    str.isEmpty() -> acc
    else -> {
        val reversed = str.head() + acc
        reverse(str.tail(), reversed)
    }
}
```

### 연습문제

> 연습문제 3-4에서 작성한 toBinary 함수가 꼬리 재귀인지 확인해보자. 만약 꼬리 재귀가 아니라면 개선해보자.

```kotlin
tailrec fun toBinary(n: Int, binary: String = ""): String = when (n) {
    0 -> binary
    else -> toBinary(n / 2, "${n % 2}$binary")
}
```

> 연습문제 3-5에서 작성한 replicate 함수가 꼬리 재귀인지 확인해보자. 만약 꼬리 재귀가 아니라면 개선해 보자.


```kotlin
tailrec fun replicate(x: Int, n: Int, result: List<Int> = listOf()): List<Int> = when (n) {
    0 -> result
    else -> replicate(x, n-1, result.plus(x))
}
```

### take 함수를 꼬리 재귀로 다시 작성하기

```kotlin
tailrec fun take(n: Int, list: List<Int>, acc: List<Int> = listOf()): List<Int> = when {
    0 >= -> acc
    list.isEmpty() -> acc
    else -> {
        val takeList = acc + listOf(list.head())
        take(n - 1, list.tail(), )
    }
} 
```

꼬리 재귀 패턴의 특징은 다음과 같다

- 누산값의 타입은 최종 반환값의 타입과 같다.

- 종료조건의 반환 값은 누산 값을 포함한 연산 결과다.

- 중간 결괏값(take 함수에서는 takeList)을 만드는 순서는 보통 '누산 값 + 새로운 값'이다.

### 연습 문제

> 연습문제 3-6에서 작성한 elem 함수가 꼬리 재귀인지 확인해보자. 만약 꼬리 재귀가 아니라면 개선해보자.

```kotlin
tailrec fun find(n: Int, list: List<Int>): Boolean = when {
    list.isEmpty() -> false
    list.first() == n -> true
    else -> find(n, list.drop(1))
} 
```

### zip 함수를 꼬리 재귀로 다시 작성하기

```kotlin
tailrec fun zip(list1: List<Int>, list2: List<Int>, acc: List<Pair<Int, Int>> = listOf()): List<Pair<Int, Int>> = when {
    list1.isEmpty() || list2.isEmpty() = -> acc
    else -> {
        val zipList = acc + list(Pair(list1.head(), list2.head()))
        zip(list1.tail(), list2.tail(), zipList)
    }
    
}
```

## 3.6 상호 재귀를 꼬리 재귀로 최적화하기

상호 재귀가 무엇인지 알아보고, 상호 재귀를 최적화하기 위한 트램펠린을 배워보자.

### 상호재귀

상호재귀는 서로 다른 두 함수가 서로 호출하는 것이다.

```kotlin
fun main() {
    println(even(9999)) // "false" 출력
    println(odd(9999)) // "true" 출력
    println(even(999999)) // StackOverflow
}

fun even(n: Int): Boolean = when (n) {
    0 -> true
    else -> odd(n - 1)
}

fun odd(n: Int): Boolean = when (n) {
    0 -> false
    else -> even(n - 1)
}
```

even(999999)는 매개변수의 값이 너무 높아서 스택 오버플로우가 발생한 것이다. 이게 발생하지 않으려면 **상호 꼬리 재귀**로 변경해야한다.

### 연습문제

> 입력값 n의 제곱근을 2로 나눈 값이 1보다 작을 때까지 반복하고, 최초의 1보다 작은 값을 반환하는 함수를 상호 재귀를 사용하여 구현하라. 이 때 입력 값 n은 2보다 크다.  
> Hint1 : 제곱근을 구하는 함수와 2로 나누는 함수를 쪼개라.  
> Hint2 : 제곱근은 java.lang.Math.sqrt() 함수를 사용하여 구할 수 있다.

```kotlin
fun sqrtRecursion(n: Double): Double = when {
    n < 1 -> n
    else -> divide(kotlin.math.sqrt(n))
}

fun divide(n: Double): Double = when {
    n < 1 -> n
    else -> sqrtRecursion(n / 2)
}
```

## 트램펄린

상호 꼬리 재귀를 가능하게 하려면 **트램펄린**을 사용하면 된다. 

- 트램펄린(trampoline) : 반복적으로 함수를 실행하는 루프
- 성크(thunk) : 트램펄린이 반복하는 함수

성크는 다음에 실핼될 함수를 매번 새로 생성하여 반환한다. 트램펄린에서 성크는 한 번에 하나만 실행된다. 프로그램을 충분히 작은 성크로 쪼갠 후 틀매펄린에서 점프하듯이 반복 실행하면 스택이 커지는 것을 막을 수 있다.

```kotlin
sealed class Bounce<A>
data class Done<A>(val result: A): Bounce<A>()
data class More<A>(val thunk: () -> Bounce<A>): Bounce<A>()

tailrec fun <A> trampoline(bounce: Bounce<A>): A = when (bounce) {
    is Done -> bounce.result
    is More -> trampoline(bounce.thunk())
}
```

트램펄린의 종료 조건이 **is Done**이고 **More**인 경우 thunk를 실행한다. 이 **thunk()**는 **값을 넘기는 것이 아니라 함수를 넘긴다** 그렇기 때문, 재귀 호출하는 시점이 아니라 실제로 값이 사용되는 시점에 값이 평가된다.


