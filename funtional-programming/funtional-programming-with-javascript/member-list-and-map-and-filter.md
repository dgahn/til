# 회원 목록, map, filter

명령형 코드로 문제를 해결해보자.

## 명령형 코드

30세 이상인 users를 거른다.

```javascript
var users = [
    { id: 1, name: 'ID', age : 36 },
    { id: 2, name: 'ID', age : 32 },
    { id: 3, name: 'ID', age : 32 },
    { id: 4, name: 'ID', age : 27 },
    { id: 5, name: 'ID', age : 25 },
    { id: 6, name: 'ID', age : 26 },
    { id: 7, name: 'ID', age : 31 },
    { id: 8, name: 'ID', age : 23 }
]

var temp_users = [];
for (var i = 0; i < users.length; i++) {
    if (users[i].age >= 30) {
        temp_users.push(users[i]);
    }
}
console.log(temp_users);
```

30세 이상인 users의 names를 수집해보자.

```javascript
var temp_users = [];
for (var i = 0; i < users.length; i++) {
    temp_users.push(users[i].age);
}
console.log(temp_users);
```

30세 미만인 users를 걸러보자.

```javascript
var temp_users = [];
for (var i = 0; i < users.length; i++) {
    if (users[i].age < 30) {
        temp_users.push(users[i]);
    }
}
console.log(temp_users);
```

30세 미만의 users의 ages를 수집해보자.

```javascript
var ages = [];
for (var i = 0; i < users.length; i++) {
    ages.push(temp_users[i].age);
}
console.log(ages);
```

위와 같은 코드를 함수형 스타일로 바꿔보도록 하겠다.

<br>

## 함수형 프로그래밍

### _filter

```javascript
var users = [
    { id: 1, name: 'ID', age : 36 },
    { id: 2, name: 'ID', age : 32 },
    { id: 3, name: 'ID', age : 32 },
    { id: 4, name: 'ID', age : 27 },
    { id: 5, name: 'ID', age : 25 },
    { id: 6, name: 'ID', age : 26 },
    { id: 7, name: 'ID', age : 31 },
    { id: 8, name: 'ID', age : 23 }
]

function _filter(users, predi) {
    var new_list = [];

    for (var i = 0; i < users.length; i++) {
        if (predi(users[i])) {
            users.push(users[i]);
        }
    }

    return new_list;
}

console.log(
    _filter(users, function(user) { return user.age >= 30; });
)

console.log(
    _filter(users, function(user) { return user.age < 30; });
)
```

함수형은 원래 값을 변경하지 않고 새로 만들어서 반환을 한다. **_filter** 는 직접 필터하는 것이 아니라 새로운 배열을 생성하여 필터한 값을 주입한다.
함수형 프로그래밍에서는 중복을 제거하거나 추상화를 할 때 함수를 통해서 한다. 조건 자체를 함수에게 위임하여 프로그래밍 하는 것이다.
이런 **_filter** 함수를 응용형 함수라고 한다. 응용형 함수는 함수가 함수를 받아서 원하는 시점에 해당하는 함수가 알고 있는 인자를 적용할 수 있다. 이를 응용형 또는 적응형 프로그래밍이라고 한다.

그리고 **_filter()** 함수를 고차함수라고도 하는데 고차함수를 인자로 받거나 함수를 반환하거나 함수 안에서 인자로 받은 함수를 실행하는 형태를 말한다.

함수형 프로그래밍의 장점은 객체 지향 프로그래밍보다 함수를 통해서 다형성을 더 추구할 수 있다는 것에 있다. 위 코드는 실제로 **users** 뿐만 아니라
일반적인 **list**에 대해서 **filter**를 할 수 있는 고차함수가 된다.

```javascript
function _filter(list, predi) {
    var new_list = [];

    for (var i = 0; i < list.length; i++) {
        if (predi(list[i])) {
            new_list.push(list[i]);
        }
    }

    return new_list;
}

console.log(_filter([1, 2, 3, 4]), function(num){ return num % 2; });
console.log(_filter([1, 2, 3, 4]), function(num){ return !(num % 2); });

```

### _map

```javascript
function _map(list, mapper) {
    var new_list = [];
    for (var i = 0; i < list.length; i++) {
        new_list.push(mapper(list[i]))
    }
    return new_list;
}

var over_30 = _filter(users, function(user) { return user.age >= 30; });
console.log(over_30);

var names = _map(over_30, function(user) { return user.name; });
console.log(names);

var under_30 = _filter(users, function(user) {return user.age < 30; });
console.log(under_30);

var ages = _map(under_30, function(user) {
    return user.age;
});
console.log(ages);
```

위 코드를 보면 데이터형이 어떻게 생겼는지가 하나도 보이지 않는다. 
데이터형이 정해지지 않고 **mapper** 함수가 어떤 함수냐에 따라서 동작하는 방식이 다양하므로 다형성이 굉장히 높다. 
그리고 list와 mapper의 관심사가 완전히 분리되어 재사용성이 극대화된다.

```javascript
console.log(
    _map(_filter(users, function(user) { return user.age >= 30;}),
    function(user) { return user.name; }));

console.log(
    _map(
        _filter(users, function(user) { return user.age < 30; }),
        function(user) { return user.age; }
    )
);
```

중간에 변수로 빼지 않으면 값이 변경되지 않기 때문에 안정성을 높일 수 있고 보다 테스트를 하기 편할 수 있다.

