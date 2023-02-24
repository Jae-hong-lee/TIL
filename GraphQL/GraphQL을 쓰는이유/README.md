# GraphQL을 쓰는 이유

### 기존 REST API 통신의 한계

1. 특정 기능을 위해 `여러번` API가 호출 됨
2. 특정 요청에 fit한 응답을 돌려주기 위해서는 API를 `새로` 만들어야함
3. API `유지보수`의 어려움

한 문장으로 요약하면, `관리해야 할 EndPoint`의 증가로 인하여 발생되는 문제점이라 할 수 있다.

> **엔드포인트** > `REST API`는 보통 여러 엔드포인트를 가지며 각각의 엔드포인트가 동일한 응답을 반환한다.
> 하지만, `GraphQL`은 보통 하나의 엔드포인트만을 사용하며 요청하는 쿼리에 따라 다른 응답을 반환하는 방식이다.

#### ex) 학교의 반, 학생의 데이터를 받아오는 예시로 둘의 차이점을 확인해보자.

```text
REST API
→ example.com/class
→ example.com/class/{반 index}
→ example.com/class/{반 index}/students
→ example.com/class/{반 index}/students/{학생 index}

GraphQL
→ example.com/graphql
(하나의 엔드포인트에 다른 쿼리를 사용해 요청)
```

`REST API`의 경우 반에 속해있는 데이터를 가져오는데에 응답마다 다양한 엔드포인트를 가지게 된다.
하지만 `GraphQL`은 하나의 엔드포인트(Root Endpoint)에 다른 쿼리로 요청함으로써 다른 응답을 받을 수 있다.
엔드포인트가 많아질 경우 관리하기 힘들 뿐더러, 많은 엔드포인트의 노출을 막기위해 추가적인 처리가 필요 할 수 있다.

### 원하는 응답 값만 받을 수 있다.

엔드포인트에 따라 정해진 응답 값만 받아올 수 있는 REST API와 달리 GraphQL은 쿼리 작성을 통해 필요한 데이터만 골라 받아올 수 있다.

스타워즈 인물에 대한 정보를 받아올 수 있는 swapi를 통해 차이점을 알아본다.

```sql
// REST API request
GET, https://swapi.dev/api/people/1

// REST API response
{
    "name": "Luke Skywalker",
    "height": "172",
    "mass": "77",
    "hair_color": "blond",
    "skin_color": "fair",
    "eye_color": "blue",
    "birth_year": "19BBY",
    "gender": "male",
    "homeworld": "http://swapi.dev/api/planets/1/",
    "films": ["http://swapi.dev/api/films/1/", "http://swapi.dev/api/films/2/", "http://swapi.dev/api/films/3/", "http://swapi.dev/api/films/6/"],
    "species": [],
    "vehicles": ["http://swapi.dev/api/vehicles/14/", "http://swapi.dev/api/vehicles/30/"],
    "starships": ["http://swapi.dev/api/starships/12/", "http://swapi.dev/api/starships/22/"],
    "created": "2014-12-09T13:50:51.644000Z",
    "edited": "2014-12-20T21:17:56.891000Z",
    "url": "http://swapi.dev/api/people/1/"
}
```

REST API를 통해서 인물의 정보를 받아오면 필요한 정보 이외에도 너무나 많은 정보까지 함께 받아야 한다.
이름,키,몸무게의 데이터만 필요하다고 할 때 GraphQL API를 사용하면 다음과 같이 요청 할 수 있다.

```graphQL

# GraphQL request
query {
    person(personID: 1) {
        name
        height
        mass
    }
}

# GraphQL response
{
    "data": {
        "person": {
            "name": "Luke Skywalker",
            "height": 172,
            "mass": 77
        }
    }
}
```

**REST API로는 3개의 데이터를 가져오기 위해 13개의 불필요한 데이터까지 함께 가져와야 했지만, GraphQL은 클라이언트에서 필요한대로 쿼리를 작성해 원하는 데이터만을 가져올 수 있다.**

---

### 1. UnderFetching

위에 내용처럼 `Rest api`는 일반적으로 url을 통해서 요청을 하고 결과값을 반환한다. 하지만 만약에 웹이 처음에 켜지고 너무 많은 요청들이 들어오면 _**서버가 과부화**_ 될 수 있다.
하지만, `GraphQL`에서는 **단 한번의 요청만**으로 페이지에 필요한 모든 데이터를 가져올 수 있다.

### 2. OverFetching

`RestApi`에서는 예를 들어 유저의 정보를 가져오는데 `get /users`를 쓸거다.
근데 클라이언트는 이름만 필요하는데 `/getNames`를 하나 새롭게 만드는 것도 불편하고 `/users`를 통해서 유저 정보 중 이름만 반환하는 것은 디비에 유저의 정보에 접근 하는데 결국 사용하는 것은 이름밖에 없기 때문에 비효율적 이다.

하지만, `GraphQL`에서는 자기가 필요한 데이터만 추출해서 받아올 수 있습니다.
그래서 `RestAPI`와는 다르게 `url`이아닌 `Query문`을 통해서 요청을 하게 됩니다.

**하지만 이러한 장점 말고 단점 존재**

1. 고정된 크기의 요청이 많을 때는 `RestAPI` 보다 요청량이 많을 수 있습니다.

2. 파일요청 등을 다룰 때에는 복잡합니다.

3. 재귀적인 Query문을 사용하지 못합니다.

그래서 텍스트 데이터나 다양한 정보가 필요한 경우에는 `GraphQL`이 더 좋을 수 도 있다.
하지만 파일 형태의 데이터를 주고 받거나 형식적인 요청이 정해져있을 때 `Rest`를 쓰는 것이 더 좋다.

---

### 정리

#### HTTP 요청 횟수를 줄일 수 있다.

RESTful의 경우 필요한 리소스 별로 요청 해야하고, 필요한 데이터들이 부분적으로 나눠서 개발되어 있다면 그만큼 요청 횟수가 늘어난다. 하지만 GraphQL은 원하는 정보를 하나의 쿼리에 모두 담아 요청 할 수 있다.

#### HTTP 응답 사이즈를 줄일 수 있다.

Restful의 경우 응답의 형태가 정해져있기 때문에 필요한 정보만 부분적으로 요청하는 것이 힘들고, 자연스럽게 데이터의 사이즈가 클 수 밖에 없다. Facebook이 GraphQL을 개발한 초기 이유 중 하나는 모바일 사용의 증가라고 한다. GraphQL을 사용함으로써 응답 데이터 사이즈를 최소화하여 모바일 환경의 부담을 줄일 수 있다.

#### 프론트엔드와 백엔드 개발자의 부담을 덜 수 있다.

Restful API를 사용한다면 프론트엔드 개발자는 API의 request/response 형식에 의존하게 된다. 따라서 새로운 엔드포인트를 효율적이게 개발하기 위해서는 프론트엔드와 백엔드 개발자의 커뮤니케이션이 강제되는 경우가 많았다.
하지만 GraphQL은 request/response 의존도가 많이 없기 때문에, 개발자들의 API 개발 부담을 덜 수 있다.

_그렇다면 GraphQL은 모든 RESTful 서비스를 대체 할 수 있을까?_ **GraphQL에도 단점존재.**

1. 고정된 요청과 응답만 필요할 때에는 query로 인해 요청의 크기가 Restful보다 커질 수 있다.
2. 캐싱이 REST보다 복잡하다.
3. 파일 업로드 구현 방법이 정해져있지 않아 직접 구현해야 한다.

---

[그래프큐엘 docs](https://graphql.org/learn/)
[GraphQL이 무엇인가?](https://velopert.com/2318)
