# 일급함수, add_maker, 함수로 함수 실행하기

## 일급 함수

**Javascript**에서는 함수를 다음과 같이 변수에 넣을 수 있다.

```javascript
var f1 = function() { return a * a; };
console.log(f1)
```

변수에 함수를 넣었기 때문에 그 결과는 함수가 나올 것이다.

<br>

함수를 인자로 받을 수 있는데 일급 함수의 중요한 특징이다.

```javascript
function f3(f) {
    return f();
}

f3(function() { return 10; });
```

위 코드를 보면 **f3** 함수에 익명 함수인 **function() { return 10; }** 을 넣었기 때문에 그 결과는 **10**이 될 것이다.
뿐만 아니라 **f3** 함수에 어떤 함수를 넣냐에 따라 그 결과가 달라질 것이다.

이러한 일급 함수라는 개념과 순수 함수라는 특징을 이용해서 함수의 조합성을 높여나가는 것이 함수형 프로그래밍이다.
언제 평가해도 상관 없는 순수 함수를 만들어 놓고 순수 함수들을 값으로 들고 다니면서 필요한 시점마다 평가를 하는 프로그래밍 기법이다.

<br>

## add_maker 예제

```javascript
function add_maker(a) {
    return function(b) {
        return a + b;
    }
}

var add10 = add_maker(10);

console.log( add10(20) );
```

**add_maker()** 를 실행하면 **10**이 들어가면서 함수를 호출하면 **function(b) { return a + b; }** 가 리턴될 것이다. 즉 함수가 리턴될 것이고
그 다음으로 **add10** 자체가 함수 이므로  **add10(20)** 을 하면 **b**에 **20**이 적용될 것이다. 

여기서 주목해야하는 것은 **function(b) { return a + b; }** 에서 **a**라는 값을 변경하지 않기 때문에 **function(b) { return a + b; }** 자체가 순수함수라는 점이다.
그렇기 때문에 **add10**을 언제 평가되어도 항상 같은 결과가 나온다.


<br>

아래의 예제를 보자

```javascript
function f4(f1, f2, ,f3) {
    return f3(f1() + f2());
}

var result = f4(
    function() { return 2; },
    function() { return 1; },
    function(a) { return a * a; }
);
```

결과는 **9**가 될 것이다. 이런식으로 함수를 인자로 넣어서 그 결과를 통해서 또 다른 결과를 내는 형식의 프로그래밍이 함수형 프로그래밍이다.

함수가 순수함수여야 유리한 점은 간략하다. 비동기적으로 실행되는 시점이나 동시성이 필요한 시점에서 특정 함수를 실행하는 시점까지 함수를 다루다가 원하는 시점에 평가를 하기 위함이다.
