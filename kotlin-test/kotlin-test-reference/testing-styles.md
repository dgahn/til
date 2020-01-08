# Testing Styles

이 스타일들에 대한 기능적인 차이는 없다. threads, tags, etc와 같은 모든 설정을 허락한다. 테스트를 어떻게 할 것이냐에 대한 선호의 차이다. 하나의 프로젝트에는 여러 개의 스타일이 들어간다.

## String Spec

**StringSpec**은 Syntax를 최소화한다. 테스트 코드를 람다 표현에 의한 문자열만 쓴다. 예제는 다음과 같다.

```kotlin
class MyTests : StringSpec({
	"strings.length should return size of string" {
		"hello".length shouldBe 5
	}
})
```

## Fun Spec

**FunSpec** 테스트를 만드는 것을 테스트를 스트링 파라미터로 설명하는 **test()** 라는 함수로 만들 수 있다. 그리고 그 테스트 자체를 람다로 만들 수 있다.

```kotlin
class MyTests : FunSpec({
    test("String length should return the length of the string") {
        "sammy".length shouldBe 5
        "".length shuldBe 0
    }
})
```

**context** 블락을 통해서 그룹핑을 할 수 있다.

```kotlin
class MyTests : FunSpec({
    context("a test group") {
        test("String length should return the length of the string") {
            "sammy".length shouldBe 5
            "".length sholudBe 0
        }
    }
})
```

## Should Spec

**ShouldSpec**은 **FunSpec**과 비슷하다. **test()** 대신 **should**를 사용한다.

```kotlin
class MyTests : ShouldSpec({
    should("return the length of the string") {
        "sammy".length sholudBe 5
        "".length sholudBe 0
    }
})
```

**context string**을 통해서 그룹핑 할 수 있다.

```kotlin
class MyTests : ShouldSpec({
    "String.length" {
        sholud("return the length of the string") {
            "sammy".length shouldBe 5
            "".length shouldBe 0
        }
    }
})
```

## Word Spec

**WordSpec**은 **should** 키워드를 사용한다. 그리고 **context string**으로 그룹핑을 할 수 있다.

```kotlin
class MyTests : WordSpec({
    "String.length" should {
        "return the length of the string" {
            "sammy".length shouldBe 5
            "".length sholudBe 0
        }
    }
})
```

**When** 키워드를 통해서 또 다른 레벨의 그룹핑을 할 수 있다.

## Feature Spec

**Feature Spec**은 **feature**와 **scenario**를 사용한다. [cucumber](https://cucumber.io/docs/gherkin/reference/)와 유사하게 사용할 수 있다. 
비록 **cucumber**를 정확하게 대신할 수 있는 없지만 키워드가 비슷하다.

## Behavior Spec

BDD Style로 작성할 수 있다. **BehaviorSpec**은 **given**, **when**, **then**을 사용할 수 있다.

```kotlin
class MyTests : BehaviorSpec({
    given("a broomstick") {
        `when`("I sit on it") {
            then("I should be able to fly") {
                // test code
            }
        }
        `when`("I throw it away") {
            then("it should come back") {
                // test code
            }
        }
    }
})
```

**when**은 Kotlin의 예약어이기 때문에 backticks를 사용해야한다. backtiks를 사용하기 싫으면 **Given**, **When**, **Then**을 사용하면 된다.

그리고 **And** 키워드를 통해서 **Given**과 **When**에 depth를 추가할 수 있다.

```kotlin
class MyTests : BehaviorSpec({
    given("a broomstick") {
        and("a witch") {
            `when`("The witch sits on it") {
                and("she laughs hysterically") {
                    then("She should be able to fly") {
                        // test code
                    }
                }
            }
        }
    }
})
```

## Free Spec

**FreeSpec**은 **-** 키워드를 통해서 다른 depth를 만들 수 있다.

```kotlin
class MyTests : FreeSpec({
    "String.length" - {
        "should return the length of the string" {
            "sammy".length shouldBe 5
            "".length shouldBe 0
        }
    }
    "containers can be nested as deep as you want" - {
        "and so we nest another container" - {
            "yet another container" - {
                "finally a real test" {
                    1 + 1 shouldBe 2
                }
            }
        }
    }
})
```

## Describe Spec

**DescribeSpec**은 Ruby background와 유사한 함수를 제공한다. 이 테스트는 Ruby test framework인 [rspec](http://rspec.info/)과 비슷하다. 이 scope는 **describe**, **context** 그리고 **it** 키워드를 사용한다.

```kotlin
class MyTests : DescribeSpec({
    describe("score") {
        it("start as zero") {
            // test here
        }
        context("with a strike") {
            it("adds ten") {
                // test here
            }
            it("carries strike to the next frame") {
                // test here
            }
        }
        
        describe("for the opposite team") {
          it("Should negate one score") {
            // test here
          }
        }
    }
})
```

## Expect Spec

**ExpectSpec**는 **context**와 **expect**를 제공한다.

```kotlin
class MyTests : ExpectSpec({
    context("a calculator") {
        expect("simple addition") {
            // test here
        }
        expect("integer overflow") {
            // test here
        }
    }
})
```

## Annotation Spec

JUnit의 스타일을 원한다면 **@Test**를 사용할 수 있다. 그리고 JUnit에서 제공하는 다음과 같은 Annotation을 사용할 수 있다.

```java
@BeforeAll / @BeforeClass
@BeforeEach / @Before
@AfterAll / @AfterClass
@AfterEach / @After
```

테스트를 무시하고 싶은 경우에는 **@Ignore**을 사용하면 된다.

```kotlin
class AnnotationSpecExample : AnnotationSpec() {

    @BeforeEach
    fun beforeTest() {
        println("Before each test")
    }

    @Test
    fun test1() {
        1 shouldBe 1
    }

    @Test
    fun test2() {
        3 shouldBe 3
    }
}
```
