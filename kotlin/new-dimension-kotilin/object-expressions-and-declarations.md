# Object Expressions and Declarations

## 1. Object
   
### 1.1 Object의 용도

어떤 class에서 조금 변경된 객체를 생성할 때 사용한다. 즉, 새로운 subclass의 명시적인 선언 없이 객체를 생성할 수 있다.

### 1.1 Object Expressions

Java의 익명 객체를 대체할 수 있다.

### 1.2 Object Declarations

언어 차원에서 싱글턴을 제공한다.

### 1.3 Companion Object

싱글턴 + static method를 만들 때 사용한다. 코틀린에서는 static이 없고 대신 패키지 내에서 함수를 선언할 수 있다.

## 2. 객체 표현식

Java에서는 익명 내부 클래스를 사용해서 처리한 것을 Kotlin에서는 어떤 클래스를 상속 받은 익명 객체를 생성해서 처리한다.

```kotlin
widndow.addMouseListener(object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) { }
    override fun mouseEntered(e: MouseEvent) { }
})
```

### 2.1 객체 표현식 상속

슈퍼타입의 생성자가 있는 경우, 반드시 값을 전달 해 주어야 한다.

슈퍼타입이 여러개인 경우 ':' 콜론 뒤에, ',' 콤마로 구분해서 명시해주면 된다.

```kotlin
open class A(x: Int) {
    public open val y: Int = x
}

interface B {...}

val ab: A = object : A(1), B {
    override val y = 15
}
```

### 2.2 객체 표현식 상속 없는 경우

특별히 상속 받을 supertypes가 없는 경우, 간단하게 작성이 가능하다.

```kotlin
val adHoc = object {
    var x: Int = 0
    var y: Int = 0
}

print (adHoc.x + adHoc.y)
```

### 2.3 객체 표현식 제약 사항

익명 객체가 local이나 private으로 선언될 때만 type으로 사용될 수 있다. 익명 객체가 public function이나 public property에서 리턴되는 경우, 익명 객체의 슈퍼타입(예제에서는 슈퍼 타입을 명시하지 않았으므로 Any)으로 동작된다. 이런 경우 익명객체에 추가된 멤버에 접근이 불가능하다.

```kotlin
class C {
    private fun foo() = object { val x: String = "x" }
    fun publicFoo() = object { val x: String = "x" }

    fun bar() {
        val x1 = foo().x // 접근 가능
        val x2 = publicFoo().x // 슈퍼 타입으로 동작해서 접근이 안된다.
    }
}
```

### 2.4 객체 표현식 특징

```kotlin
fun countClicks(window: JComponent) {
    var clickCount = 0
    var enterCount = 0

    window.addMouseListener(object : MouseAdapter() {
        override fun mouseClicked(e: MouseEvent) {
            clickCount++ // 위의 변수를 쉽게 접근할 수 있다. 원래 Java에서는 final이어야지만 접근할 수 있다.
        }

        override fun mouseEntered(e: MouseEvent) {
            enterCount++
        }
    })
}
```

익명 객체의 코드는 enclosing scope의 변수를 접근할 수 있다. 

## 3. 객체 선언 용도

object declarations을 통해서 sigleton 패턴을 만들 수 있다.

```kotlin
object DataProviderManager {
    fun registerDataProvider (provider: DataProvider) { }
    val allDataProviders: Collection<DataProvider>
}
```

- object 키워드 뒤에 항상 이름이 있어야한다.
- object declaration은 object expression이 아니다.
- 그래서 할당 구문의 우측에 사용될 수가 없다.

object declaration의 객체를 참조하려면, 해당 이름으로 직접 접근하면 된다.

```kotlin
DataProvviderManager.registerDataProvider(...)
```

### 3.1 객체 선언 문법

슈퍼타입을 가질 수 있다. (상속이 가능하다는 이야기)

```kotlin
object DefaultListener : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) { }
    override fun mouseEntered(e: MouseEvent) { }
}
```

## 4. Companion Object

클래스 내부의 object declaration은 companion 키워드를 붙일 수 있다.

```kotlin
class MyClass {
    companion object Factory {
        fun create() : MyClass = MyClass()
    }
}
```

companion object의 멤버를 멤버 클래스 이름을 통해서 호출할 수 있다.

```kotlin
val instance = MyClass.create()
```

<br>

```kotlin
class MyClass {
    companion object {

    }
}

val x = MyClass.Companion
```

companion object의 이름은 생략될 수 있다. 이런 경우 [class name].Companion 형태로 객체에 접근이 가능하다.


```kotlin
interface Factory<T> {
    fun create(): T
}

class MyClass {
    companion object : Factory<MyClass> {
        override fun create(): MyClass = MyClass()
    }
}
```

companion object의 멤버가 다른 언어의 static 멤버처럼 보일 수 있지만 아니다. companion object의 멤버는 실제 객체의 멤버이고 슈퍼클래스도 가질 수 있는 클래스의 객체가 된다.

## 5. object expression(객체 표현식) vs obejct declaration(객체 선언)

- object exrepssions는 즉시 초기화 되고 실행된다.


```kotlin
fun main(args: Array<String>) {
    println(1)
    println(MyClass_1)
    println(2)
    println(MyClass_2)
    println(3)
    println(MyClass_3)
    println(4)
}

object MyClass_1 {
    init {
        println("create MyClass_1")
    }
}
object MyClass_2 {
    init {
        println("create MyClass_2")
    }
}
object MyClass_3 {
    init {
        println("create MyClass_3")
    }
}
```

- object declarations는 나중에 초기화 된다.

- companion object는 클래스가 로드될 때 초기화 된다. java static initializer와 매칭된다.