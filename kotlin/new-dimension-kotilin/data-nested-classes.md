# Data, Nested classes

## 1. Data 클래스

데이터는 보유하지만 아무것도 하지 않는 클래스를 의미한다.

```kotlin
data class User(val name: String, val age: Int)
```

data라는 키워드로 생성이 가능하다.

기본 생성자에서 다음과 같은 기능들을 컴파일러가 자동으로 생성해주는 것이 특징이다.

- equals()
- hashCode()
- copy()
- toString() of the form "User(name=John, age=42)"
- componentN() functions

명시적으로 정의해주는 경웨는, 컴파일러가 자동으로 생성해주지 않는다.

### 1.1 의미 있는 Data 클래스의 조건

- 기본 생성자에 1개 이상의 파라미터
- 기본 생성자의 파라미터가 var, val로 선언
- Data 클래스는 abstract, open, sealed, inner가 안된다.
- Data 클래스에 interface 구현이 가능하다.
- Sealed class를 상속할 수 있다.

### 1.2 기본 값

- JVM에서 파라미터 없는 생성자가 필요한 경우 모든 프로퍼티에 기본 값을 설정해주면 된다.

```kotlin
data class User(val name: String = "", val age: Int = 0)
```

```kotlin
val exam_0 = User()
val exam_1 = User("Kotlin")
val exam_2 = User("Kotlin", 113)
val exam_3 = User(age=113)
val exam_4 = User(name="Kotlin", age=113)
```

### 1.3 복사

객체의 기존 값들은 유지하고, 일부 값만 고쳐서 새로운 객체를 만들고 싶은 경우를 말한다. Data 클래스에 컴파일러가 copy를 만들어 주기 때문에 바로 copy를 호출해서 사용하면 된다.

```kotlin
fun copy(name: String = this.name, age: Int = this.age) = User(name, age)
```

```kotlin
val jack = User(name = "Jack", age = 1)
val olderJack = jack.copy(age = 2)
```

### 1.4 Destructuring Declarations

data 클래스는 Destructuring Declarations 사용이 가능하다. 컴파일러가 componentN 함수를 자동으로 만들어 주기 때문이다.

```kotlin
val jane = User("Jane", 35)
val (name, age) = jane
println("$name, $age years of age")
```

### 1.5 Standard Data Classes

스탠다드 라이브러리에서 제공하는 data 클래스도 있다.
- Pair
- Triple

물론 가독성을 위해서는 프로퍼티에 의미 있는 이름을 제공하는 클래스가 최고이다. 간단하게 무엇을 하는 경우 사용하면 좋다.

```kotlin
val jane = User("Jane", 35)
println(jane) // User(name=Jane, age=35)

val pair = Pair("Jane", 35)
println(pair) // (Jane, 35)
```

## 2. Nested Classes

클래스는 다른 클래스에 중첩될 수 있다.

```kotlin
class Outer {
    private val bar: Int = 1

    class Nested {
        fun foo() = 2
    }
}

val demo = Outer.Nested().foo()
```

### 2.1 inner

클래스에 inner를 표기하면 바깥쪽 클래스의 멤버에 접근 할 수 있다.

```kotlin
class Outer {
    private val bar: Int = 1
    inner class Inner {
        fun foo() = bar
    }
}

val demo = Outer().Inner().foo() // == 1
```

### 2.2 익명 내부 클래스 (Anonymous inner class)

객체 표현식 (object expression)을 이용해서 익명 내부 클래스의 인스턴스를 생성할 수 있다.

```kotlin
mSearchEditText.setOnClinckListener(object: View.OnClickListener{
    override fun onClick(v: View?) {

    }
})
```

functional java interface인 경우에는 접두에 인터페이스 이름을 사용해서 람다식으로 표현할 수 있다.

```kotlin
mSearchEdtiText.setOnClickListener(View.OnClickListener{
    v -> // ...
})
```