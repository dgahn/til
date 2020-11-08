## 쿼리 & 뮤테이션

이 글에서는 GraphQL 서버에서 쿼리하는 방법에 대해 요약한다.

<br/>

### 필드

GraphQL은 내가 원하는 객체의 필드를 지정하여 요청할 수 있다. 다음은 아주 간단한 쿼리를 실행하여 객체의 필드 값을 얻는 예제다.

#### Query
```
{
    hero {
        name
    }
}
```
#### Result
```
{
    "data": {
        "hero": {
            "name": "R2-D2"
        }
    }
}
```

쿼리와 얻은 결과가 동일한 것이 GraphQL의 핵심이다. 필드는 String 같은 특정 타입이 아니라 객체일 수 도 있다.
#### Query

```
{
    hero {
        name
        friends {
            name
        }
    }
}
```
#### Result
```
{
  "data": {
    "hero": {
      "name": "R2-D2",
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
} 
```

<br/>

### 인자

인자를 넣으면 특정 객체를 가져올 수 있다.(필터, 정렬 등등)

#### Query
```
{
  human(id: "1000") {
    name
    height
  }
}
```
#### Result
```
{
  "data": {
    "human": {
      "name": "Luke Skywalker",
      "height": 1.72
    }
  }
}
```

뿐만 아니라 하위 객체의 값도 인자를 추가할 수 있다.
#### Query
```
{
  human(id: "1000") {
    name
    height(unit: FOOT)
  }
}
```
#### Result
```
{
  "data": {
    "human": {
      "name": "Luke Skywalker",
      "height": 5.6430448
    }
  }
}
```

<br/>

### 별칭

별칭을 통해서 결과를 원하는 이름으로 바꿀 수 있다.
#### Query
```
{
  empireHero: hero(episode: EMPIRE) {
    name
  }
  jediHero: hero(episode: JEDI) {
    name
  }
}
```
#### Result
```
{
  "data": {
    "empireHero": {
      "name": "Luke Skywalker"
    },
    "jediHero": {
      "name": "R2-D2"
    }
  }
}
```

결과를 보면 알겠지만 하나의 요청에서 다른 타입들을 구별해서 요청할 수 있다.

<br/>

### 프래그먼트

내가 원하는 요청을 여러번 써야하는 경우 간단하게 다음과 같이 작성할 수 있다.


#### Query
```
{
  leftComparison: hero(episode: EMPIRE) {
    ...comparisonFields
  }
  rightComparison: hero(episode: JEDI) {
    ...comparisonFields
  }
}
​
fragment comparisonFields on Character {
  name
  appearsIn
  friends {
    name
  }
}
```
#### Result
```
{
  "data": {
    "leftComparison": {
      "name": "Luke Skywalker",
      "appearsIn": [
        "NEWHOPE",
        "EMPIRE",
        "JEDI"
      ],
      "friends": [
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        },
        {
          "name": "C-3PO"
        },
        {
          "name": "R2-D2"
        }
      ]
    },
    "rightComparison": {
      "name": "R2-D2",
      "appearsIn": [
        "NEWHOPE",
        "EMPIRE",
        "JEDI"
      ],
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```

<br/>

#### 프래그먼트 안에서 변수 사용하기

쿼리나 뮤테이션에 선언된 변수는 프래그먼트에 접근할 수 있다.

#### Query
```
query HeroComparison($first: Int = 3) {
  leftComparison: hero(episode: EMPIRE) {
    ...comparisonFields
  }
  rightComparison: hero(episode: JEDI) {
    ...comparisonFields
  }
}
​
fragment comparisonFields on Character {
  name
  friendsConnection(first: $first) {
    totalCount
    edges {
      node {
        name
      }
    }
  }
}
```
#### Result
```
{
  "data": {
    "leftComparison": {
      "name": "Luke Skywalker",
      "friendsConnection": {
        "totalCount": 4,
        "edges": [
          {
            "node": {
              "name": "Han Solo"
            }
          },
          {
            "node": {
              "name": "Leia Organa"
            }
          },
          {
            "node": {
              "name": "C-3PO"
            }
          }
        ]
      }
    },
    "rightComparison": {
      "name": "R2-D2",
      "friendsConnection": {
        "totalCount": 3,
        "edges": [
          {
            "node": {
              "name": "Luke Skywalker"
            }
          },
          {
            "node": {
              "name": "Han Solo"
            }
          },
          {
            "node": {
              "name": "Leia Organa"
            }
          }
        ]
      }
    }
  }
}
```

<br/>

### 작업 이름

실제 사용할 때는 **query** 키워드와 **query** 이름을 넣어서 헷갈리지 않게 작성하는 것이 좋다.

다음은 작업 타입이 **query**, 작업 이름이 **HeroNameAndFreinds**로 작성한 예제다.

#### Query
```
query HeroNameAndFriends {
  hero {
    name
    friends {
      name
    }
  }
}
```
#### Result
```
{
  "data": {
    "hero": {
      "name": "R2-D2",
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```

#### 작업 타입
  - **쿼리**(query), **뮤테이션**(mutation), **구독**(subscription)
  - 어떤 작업의 타입인지 기술한다.
  - 작업 타임과 이름은 **Graph 요청을 식별하는데 매우 유용한 디버깅 도구**가 될 수 있다.

<br/>

### 변수

변수를 사용하는 방법은 다음과 같다.
  1. 쿼리안의 정적 값을 **$variableName**으로 변경한다.
  2. **$variableName**을 쿼리에서 받는 변수로 선언하다.
  3. 별도의 전송규약 변수에 **variableName: value**을 전달한다.

예제는 다음과 같다.

#### Query
```
query HeroNameAndFriends($episode: Episode) {
  hero(episode: $episode) {
    name
    friends {
      name
    }
  }
}

variables
{
  "episode": "JEDI"
}
```

#### Result
```
{
  "data": {
    "hero": {
      "name": "R2-D2",
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```

<br/>

### 변수 기본값

타입 선언 다음에 기본 값을 명시할 수 있다.
```
query HeroNameAndFriends($episode: Episode = "JEDI") {
  hero(episode: $episode) {
    name
    friends {
      name
    }
  }
}
```

<br/>

### 지시어

필드를 **지시어**를 통해서 선택할 수 있다.

#### Query
```
query Hero($episode: Episode, $withFriends: Boolean!) {
  hero(episode: $episode) {
    name
    friends @include(if: $withFriends) {
      name
    }
  }
}

variables
{
  "episode": "JEDI",
  "withFriends": false
}
```
#### Result
```
{
  "data": {
    "hero": {
      "name": "R2-D2"
    }
  }
}
```

기본적인 지시어는 다음과 같다.
  - @include(if: Boolean): 인자가 true 인 경우에만 이 필드를 결과에 포함합니다.
  - @skip(if: Boolean) 인자가 true 이면 이 필드를 건너뜁니다.


### 뮤테이션
서버 측의 데이터를 수정하는 작업은 **뮤테이션**이라고 부른다.

간단한 뮤테이션의 예제는 다음과 같다.

#### Query
```
mutation CreateReviewForEpisode($ep: Episode!, $review: ReviewInput!) {
  createReview(episode: $ep, review: $review) {
    stars
    commentary
  }
}

variables
{
  "ep": "JEDI",
  "review": {
    "stars": 5,
    "commentary": "This is a great movie!"
  }
}
```
#### Result
```
{
  "data": {
    "createReview": {
      "stars": 5,
      "commentary": "This is a great movie!"
    }
  }
}
```

뮤테이션과 쿼리에는 특별한 차이가 있는데, 쿼리 필드는 병렬로 실행되지만 뮤테이션 필드는 하나씩 차례대로 실행이 된다.  즉 하나의 요청에서
두 개의 뮤테이션을 보내면 첫 번째는 두 번째 요청 전에 반드시 완료 되는 것이 보장 된다.

### 인라인 프래그먼트

-- 추후 추가

### 메타 필드

-- 추후 추가