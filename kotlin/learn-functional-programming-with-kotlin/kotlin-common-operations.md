# 코틀린 컬렉션 함수

이 문서는 [코틀린 레퍼런스](https://kotlinlang.org/docs/reference/collection-operations.html)를 참고하였습니다.

## Transformations

### Mapping
**mapping**은 콜렉션 요소를 함수의 결과에 따라 다른 콜렉션으로 변경시킨다. 이 때 다른 콜렉션은 **리스트**다.

```kotlin
val numbers = setOf(1, 2, 3)
println(numbers.map { it * 3 }) // [3, 6, 9]
println(numbers.mapIndexed { idx, value -> value * idx }) // [0, 2, 6]
```

중간에 **null**을 생성하는 요소가 있어 제외하고 싶다면 **mapNotNull()** 을 사용하면 된다.

```kotlin
val numbers = setOf(1, 2, 3)
println(numbers.mapNotNull { if (it == 2) null else it * 3}) // [3, 9]
println(numbers.mapIndexedNotNull { idx, value -> if (idx == 0) null else value * idx }) // [2, 6]
```

**map** 안에 있는 요소를 **mapKeys()**와 **mapValues()** 변경 시킬 수 있다. 반환되는 컬렉션은 **map**이다.

```kotlin
val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
println(numbersMap.mapKeys { it.key.toUpperCase() }) // {KEY1=1, KEY2=2, KEY3=3, KEY11=11}
println(numbersMap.mapValues { it.value + it.key.length }) // {key1=5, key2=6, key3=7, key11=16}
```

### Zipping

두 컬렉션의 같은 인덱스에 있는 값들을 **Pair** 리스트로 만든다.

```kotlin
val colors = listOf("red", "brown", "grey")
val animals = listOf("fox", "bear", "wolf")
println(colors zip animals) // [(red, fox), (brown, bear), (grey, wolf)]

val twoAnimals = listOf("fox", "bear")
println(colors.zip(twoAnimals)) // [(red, fox), (brown, bear)]
```

두 번째 인자로 람다를 넣으면 **Pair** 값을 사용하여 변경된 다른 객체를 반환한다.

```kotlin
val colors = listOf("red", "brown", "grey")
val animals = listOf("fox", "bear", "wolf")

println(colors.zip(animals) { color, animal -> "The ${animal.capitalize()} is $color" })
// [The Fox is red, The Bear is brown, The Wolf is grey]
```

**unzip**을 통해서 **pair**의 리스트를 두 개의 리스트로 나눌 수 있다.

```kotlin
val numberPairs = listOf("one" to 1, "two" to 2, "threee" to 3, "four" to 4)
println(numberPairs.unzip()) // ([one, two, threee, four], [1, 2, 3, 4])
```

### Association

컬렉션의 요소를 관계 되는 값과 **map**으로 만들 수 있다. **associateWith**는 컬렉션의 값을 키 값으로 가지고 람다의 반환 값을 value 값으로 가진다.

```kotlin
val numbers = listOf("one", "two", "three", "four")
println(numbers.associateWith { it.length }) // {one=3, two=3, three=5, four=4}
```

**associateBy**는 컬렉션의 값을 value 값으로 가지고 람다의 반환 값을 키 값으로 가진다.

```kotlin
val numbers = listOf("one", "two", "three", "four")

println(numbers.associateBy { it.first().toUpperCase() }) // {O=one, T=three, F=four}
println(numbers.associateBy(keySelector = { it.first().toUpperCase() }, valueTransfor = { it.length })) // {O=3, T=5, F=4}
```

**associate**는 key 값과 value 값을 직접 지정할 수 있다.

```kotlin
val names = listOf("Alice Adams", "Brian Brown", "Clara Campbell")
println(names.associate { name -> name.split(" ").let { it[0] to it[1] } }) // {Alice=Adams, Brian=Brown, Clara=Campbell}
```

### Flattening

중첩된 컬렉션을 풀어낼 수 있다.

```kotlin
val numberSets = listOf(setOf(1, 2, 3), setOf(4, 5, 6), setOf(1, 2))
println(numberSets.flatten()) // [1, 2, 3, 4, 5, 6, 1, 2]
```

풀어내려는 것이 객체 안의 컬렉션이면 **flatMap**을 통해서 풀어낼 수 있다.

```kotlin
val pairs = listOf(Pair(listOf("1", "2"), "2"), Pair(listOf("3", "4"), "2"), Pair(listOf("5", "6"), "2"))
println(pairs.flatMap {it.first}) // [1, 2, 3, 4, 5, 6]
```

### String representation

리스트에 있는 String을 합칠 수 있다.

```kotlin
val numbers = listOf("one", "two", "three", "four")

println(numbers) // [one, two, three, four]
println(numbers.joinToString()) // one, two, three, four

val listString = StringBuffer("The list of numbers: ")
numbers.joinTo(listString)
println(listString) // The list of numbers: one, two, three, four
```

리스트에 있는 String을 합칠 때, **separator**, **prefix**, **postfix**등을 줄 수 있다.

```kotlin
val numbers = listOf("one", "two", "three", "four")
println(numbers.joinToString(separator = " | ", prefix = "start: ", postfix = ": end")) // start: one | two | three | four: end
```

String을 합칠 때, **limit**으로 합칠 String을 제한할 수 있다. 여기서 나머지 값들은 **...** 으로 표기되는데
**truncated**를 통해서 나머지를 어떻게 표기할지 결정할 수 있다. 

```kotlin
val numbers = (1..100).toList()
println(numbers.joinToString(limit = 10)) // 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, ...
println(numbers.joinToString(limit = 10, truncated = "등등")) // 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 등등
```

람다식을 통해서 변경해서 합칠 수 있다.
```kotlin
val numbers = listOf("one", "two", "three", "four")
println(numbers.joinToString {"Element: ${it.toUpperCase()}"})  // Element: ONE, Element: TWO, Element: THREE, Element: FOUR
```

## Filtering

### Filtering by predicate

람다식에 **true**가 된 값만 추출할 수 있다.

```kotlin
val numbers = listOf("one", "two", "three", "four")
val longerThan3 = numbers.filter { it.length > 3 }
println(longerThan3) // [three, four]

val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
val filteredMap = numbersMap.filter { (key, value) -> key.endsWith("1") && value > 10  }
println(filteredMap) // {key11=11}
```

람다식에 **false**가 된 값만 추출할 수 있다.

```kotlin
val numbers -> listOf("one", "two", "three", "four")

val filteredIdx = numbers.filterIndexed { index, s -> (index != 0) && (s.length < 5) }
val filteredNot = numbers.filterNot { it.length <= 3 }

println(filteredIdx) // [two, four]
println(filteredNot) // [three, four]
```

제네릭 타입이 무엇이냐에 따라 filter를 할 수도 있다.

```kotlin
val numbers = listOf(null, 1, "two", 3.0, "four")
println("All String elements in upper case:") // All String elements in upper case:
numbers.filterIsInstance<String>().forEach { 
    println(it.toUpperCase()) // TWO FOUR
}
```

**Null**이 아닌 것을 분류할 수 있다.

```kotlin
val numbers = listOf(null, "one", "two", null)
println(numbers.filterNotNull()) // [one, two]
```

### Partitioning

람다식의 조건이 **true**인 것과 **false**인 것으로 분류할 수 있다.

```kotlin
val numbers = listOf("one", "two", "three", "four")
val (match, rest) = numbers.partition { it.length > 3 }

println(match) // [three, four]
println(rest) // [one, two]
```

### Testing predicates

- any() : 적어도 하나라도 있으면 true를 반환한다.
- none() : 하나도 없는 경우 true를 반환한다.
- all() : 모두 있는 경우 true를 반환한다.

```kotlin
val numbers = listOf("one", "two", "three", "four")

println(numbers.any { it.endsWith("e") })
println(numbers.none { it.endsWith("a") })
println(numbers.all { it.endsWith("e") })

println(emptyList<Int>().all { it > 5 }) // true, 빈 배열과 사용하면 true를 반환한다. vacuous truth ??
```

- any() : 하나라도 있으면 true를 반환한다.
- none() : 없으면 true를 반환한다.

```kotlin
val numbers = listOf("one", "two", "three", "four")
val empty = emptyList<String>()
println(numbers.any())
println(empty.any())

println(numbers.none())
println(empty.none())
```

## plus and minus Operators

코틀린에서 제공되는 불가변 자료구조에 대해서 요소를 더하거나 뺀 리스트를 새로 만들 수 있다.

```kotlin
val numbers = listOf("one", "two", "three", "four")
val plusList = numbers + "five"
val minusList = numbers - listOf("three", "four")

println(plusList) // [one, two, three, four, five]
println(minusList) // [one, two]
```

```kotlin
val numbers = listOf("one", "two", "three", "four")
val plusList = numbers.plus("five")
val minusList = numbers.minus(listOf("three", "four"))

println(plusList)
println(minusList)
```

## Grouping

Map으로 변환시켜준다.

```kotlin
val numbers = listOf("one", "two", "three", "four", "five")

println(numbers.groupBy { it.first().toUpperCase() }) // {O=[one], T=[two, three], F=[four, five]}
println(numbers.groupBy(keySelector = { it.first() }, valueTransform = { it.toUpperCase() })) // {o=[ONE], t=[TWO, THREE], f=[FOUR, FIVE]}
```

**groupingBy()** 를 사용하면 다른 연산을 통해서 가공된 값을 반환할 수 있다.

```kotlin
val numbers = listOf("one", "two", "three", "four", "five", "six")
println(numbers.groupingBy { it.first() }.eachCount())
```

## Retrieving Collection Parts

### Slice

```kotlin
val numbers = listOf("one", "two", "three", "four", "five")

println(numbers.slice(1..3)) // [two, three, four]
println(numbers.slice(0..4 step 2)) // [one, three, five]
println(numbers.slice(setOf(3, 5, 0)))  // [four, six, one]
```

### Take and drop

앞에서 또는 뒤에서부터 값을 가져오거나 삭제할 수 있다.

```kotlin
val numbers = listOf("one", "two", "three", "four", "five")

println(numbers.take(3)) // [one, two, three]
println(numbers.takeLast(3)) // [three, four, five]
println(numbers.drop(1)) // [two, three, four, five]
println(numbers.dropLast(2)) // [one, two, three]
```

특정 조건을 만족하는 동안 앞에서 또는 뒤에서부터 값을 가져오거나 삭제할 수 있다.

```kotlin
val numbers = listOf("one", "two", "three", "four", "five")

println(numbers.takeWhile { !it.startsWith('f') }) // [one, two, three]
println(numbers.takeLastWhile { it != "three" }) // [four, five]
println(numbers.dropWhile { it.length == 3 }) // [three, four, five]
println(numbers.dropLastWhile { it.contains('i') }) // [one, two, three, four]
```

### Chunked

리스트를 특정 단위로 묶을 수 있다.

```kotlin
val numbers = (0..13).toList()
println(numbers.chunked(3)) // [[0, 1, 2], [3, 4, 5], [6, 7, 8], [9, 10, 11], [12, 13]]
```

특정 단위로 묶은 다음에 연산을 한 것을 리스트화 할 수 있다.

```kotlin
val numbers = (0..13).toList()
println(numbers.chunked(3) { it.sum() }) // [3, 12, 21, 30, 25]
```

### Windowed

인덱스를 올리면서 특정 단위로 묶을 수 있다.

```kotlin
val numbers = listOf("one", "two", "three", "four", "five")
println(numbers.windowed(3)) // [[one, two, three], [two, three, four], [three, four, five]]
```

**step**을 통해서 올리는 인덱스 값을 조정할 수 있다. 그리고 **partialWindows**을 통해서 단위에 만족하지 않는 것을 버리거나 포함할 수 있다.

```kotlin
val numbers = (1..10).toList()
println(numbers.windowed(3, step = 2, partialWindows = true)) // [[1, 2, 3], [3, 4, 5], [5, 6, 7], [7, 8, 9], [9, 10]]
println(numbers.windowed(3, step = 2, partialWindows = false)) // [[1, 2, 3], [3, 4, 5], [5, 6, 7], [7, 8, 9]]
println(numbers.windowed(3) { it.sum() }) // [6, 9, 12, 15, 18, 21, 24, 27]
```

현재 인덱스의 값과 다음 인덱스의 값을 묶어서 **Pair** 리스트를 만들 수 있다.

```kotlin
val numbers = listOf("one", "two", "three", "four", "five")
println(numbers.zipWithNext())
println(numbers.zipWithNext() { s1, s2 -> s1.length > s2.length })
```

## Retrieving Single Elements

### Retrieving by Position

특정 위치의 요소를 검색할 수 있다.

```kotlin
val numbers = listOf("one", "two", "three", "four", "five")
println(numbers.elementAt(3)) // four

val numbersSortedSet = sortedSetOf("one", "two", "three", "four")
println(numbersSortedSet.elementAt(0)) // four
```

첫번째와 마지막 요소에 해당하는 함수가 있다.

```kotlin
val numbers = listOf("one", "two", "three", "four", "five")
println(numbers.first())  // one
println(numbers.last())   // five
```

넘어간 인덱스에 대한 처리를 할 수가 있다.

```kotlin
val numbers = listOf("one", "two", "three", "four", "five")
println(numbers.elementAtOrNull(5))  // null 
println(numbers.elementAtOrElse(5) { index -> "The value for index $index is undefined"}) // The value for index 5 is undefined
```

### Retrieving by Condition

맨 처음 인덱스부터 검색하여 첫번째로 만족하는 리스트의 값을 가져올 수 있다.

```kotlin
val numbers = listOf("one", "two", "three", "four", "five", "six")
println(numbers.first { it.length > 3 }) // three
println(numbers.last { it.startsWith("f") }) // five
```

맞는 조건이 없으면 Null을 반환하는 함수가 있다.

```kotlin
val numbers = listOf("one", "two", "three", "four", "five", "six")
println(numbers.firstOrNull { it.length > 6 }) // null
println(numbers.lastOrNull { it.length > 6 }) // null
```

firstOrNull -> find(), lastOrNul -> findLast()로 사용할 수 있다.

```kotlin
val numbers = listOf("one", "two", "three", "four", "five", "six")
println(numbers.find { it.length > 6 })
println(numbers.findLast { it.length > 6 })
```

### Random Element

컬렉션의 요소 중에 랜덤으로 하나 가져올 수 있다.

```kotlin
val numbers = listOf(1, 2, 3, 4)
println(numbers.random()) // 3
```

### Checking existence

- contains : 하나가 포함되는지
- containsAll : 컬렉션의 값이 모두 포함하는지

```kotlin
val numbers = listOf("one", "two", "three", "four", "five", "six")
println(numbers.contains("four")) // true
println("zero" in numbers) // false

println(numbers.containsAll(listOf("four", "two"))) // true
println(numbers.containsAll(listOf("one", "zero"))) // false
```

## Collection Ordering

기본적인 sorting은 숫자의 경우 오름 차순으로 글자의 경우 영어사전 순으로 나열된다.

```kotlin
println(listOf("aaa", "bb", "c").sorted()) // [aaa, bb, c]
println(listOf(5, 2, 9).sorted()) // [2, 5, 9]
```

별도로 연산을 하고 싶은 경우 비교 연산을 선언할 수 있다. Comparator에 들어가는 결과물이 양수가 되게 하는 것이 차순을 결정한다.
예를 들어, 내림 차순의 경우 뒤의 숫자가 작음으로 뒤에서 앞에서 뒤를 빼야 양수를 만들 수 있다.

```kotlin
val lengthComparator = Comparator { str1: String, str2: String -> str1.length - str2.length }
println(listOf("aaa", "bb", "c").sortedWith(lengthComparator)) // [c, bb, aaa]
```

**compareBy**로 간단하게 표현할 수도 있다. 기본적으로 오름 차순이다.

```kotlin
println(listOf("aaa", "bb", "c").sortedWith(compareBy { it.length })) // [c, bb, aaa]
```

### Natural Order

오름 차순, 내림 차순의 기본정렬 함수를 제공한다.

```kotlin
val numbers = listOf("one", "two", "three", "four")

println("Sorted ascending: ${numbers.sorted()}") // [four, one, three, two]
println("Sorted descending: ${numbers.sortedDescending()}") // [two, three, one, four]
```

### Custom orders

사용자 지정 순서나 비교할 수 없는 객체를 정렬하기 위해 **sortedBy()** 및 **sortedByDescending()** 함수를 제공한다

```kotlin
val numbers = listOf("one", "two", "three", "four")

val sortedNumbers = numbers.sortedBy { it.length }
println("Sorted by length ascending: $sortedNumbers") // [one, two, four, three]
val sortedByLast = numbers.sortedByDescending { it.last() }
println("Sorted by the last letter descending: $sortedByLast") // [four, two, one, three]
```

**Comparator**를 통해서 구현하는 **sortedWith()**를 제공한다.

```kotlin
val numbers = listOf("one", "two", "three", "four")
println("Sorted by length ascending: ${numbers.sortedWith(compareBy { it.length })}") // [one, two, four, three]
```

### Reverse order

순서를 반대로 변경해주는 **reversed()** 를 제공한다

```kotlin
val numbers = listOf("one", "two", "three", "four")
println(numbers.reversed()) // [four, three, two, one]
```

뷰 형태의 **asReversed()** 를 제공한다.

```kotlin
val numbers = listOf("one", "two", "three", "four")
val reversedNumbers = numbers.asReversed()
println(reversedNumbers) // [four, three, two, one]
```

**asReversed()** 는 원본이 바뀌면 자동으로 바뀐다.

```kotlin
val numbers = mutableListOf("one", "two", "three", "four")
val reversedNumbers = numbers.asReversed()
println(reversedNumbers) // [four, three, two, one]
numbers.add("five")
println(reversedNumbers) // [five, four, three, two, one]
```

### Random order

랜덤으로 섞어 주는 **shuffled()** 를 제공한다.

```kotlin
val numbers = listOf("one", "two", "three", "four")
println(numbers.shuffled())
```

## Collection Aggregate Operation

기본적인 집합 연산을 제공한다.

```kotlin
val numbers = listOf(6, 42, 10, 4)

println("Count: ${numbers.count()}") // Count: 4
println("Max: ${numbers.max()}") // Max: 42
println("Min: ${numbers.min()}") // Min: 4
println("Average: ${numbers.average()}") // Average: 15.5
println("Sum: ${numbers.sum()}") // Sum: 62
```

특정 조건으로 집합 연산을 할 수 있도록 제공한다.

```kotlin
val numbers = listOf(5, 42, 10, 4)
val min3Remainder = numbers.minBy { it % 3 }
println(min3Remainder) // 42

val strings = listOf("one", "two", "three", "four")
val longestString = strings.maxWith(compareBy { it.length }) 
println(longestString) // three

println(numbers.sumBy { it * 2 }) // 122
println(numbers.sumByDouble { it.toDouble() / 2 }) // 30.5
```

### Fold And Reduce

컬렉션 요소들에 대해서 특정 연산을 실행 시켜주는 **fold()** 와 **reduce()** 를 제공한다.

**fold()** 는 직접 초기값을 지정해주고 **reduce()** 는 첫번째 인자를 초기값으로 둔다.

```kotlin
val numbers = listOf(5, 2, 10, 4)

val sum = numbers.reduce { sum, element -> sum + element } // 첫번째 단계에서 sum이 초기값으로 5, element가 2
println(sum) // 21
val sumDoubled = numbers.fold(0) { sum, element -> sum + element * 2 }
println(sumDoubled) // 42
```

마지막 인덱스부터 연산하는 **foldRight()** 와 **reduceRight()** 를 제공한다.

```kotlin
val numbers = listOf(5, 2, 10, 4)
val sumDoubledRight = numbers.foldRight(0) { element, sum -> sum + element * 2 }
println(sumDoubledRight)
```

인덱스와 함께 사용할 수 있는 **foldIndexed()** **foldRightIndexed()** **reduceIndexed()** **reduceRightIndexed()** 가 제공된다.

```kotlin
val numbers = listOf(5, 2, 10, 4)
val sumEven = numbers.foldIndexed(0) { idx, sum, element -> if (idx % 2 == 0) sum + element else sum }
println(sumEven) // 42

val sumEvenRight = numbers.foldRightIndexed(0) { idx, element, sum -> if (idx % 2 == 0) sum + element else sum }
println(sumEvenRight) // 15
```