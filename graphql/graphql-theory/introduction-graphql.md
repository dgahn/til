## GraphQL 소개

GraphQL은 API를 내가 원하는 값만 가져올 수 있게 구현한 서버 사이드 실행 환경이다. 예를 들어, 데이터가 서버에 다음과 같이 정의되어 있다고 가정해보자.

```
type Query {
  user: User
}

type User {
  id: ID
  name: String
}
```

graphql을 통해서 쿼리를 하면 user에서 name만 가져올 수 있다. 다음과 같이 쿼리를 하면

```
{
    me {
        name
    }
}
```

결과는 다음과 같다.

```
{
    "me": {
        "name": "dgahn"
    }
}
```