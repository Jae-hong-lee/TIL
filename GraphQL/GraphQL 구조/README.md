# GraphQL 구조

## 쿼리/뮤테이션(query/mutation)

쿼리와 뮤테이션 그리고 응답 내용의 구조는 상당히 직관적임, 요청하는 쿼리문의 구조와 응답 내용의 구조는 거의 일치 한다.
![GraphQL 쿼리문(좌측)과 응답 데이터 형식(우측)](http://tech.kakao.com/files/graphql-example.png)
_GraphQL 쿼리문(좌측)과 응답 데이터 형식(우측)_

쿼리와 뮤테이션 그리고 응답 내용의 구조는 상당히 직관적이고, 요청하는 쿼리문의 구조와 응답 내용의 구조는 거의 일치한다.

gql에서는 굳이 `쿼리(query)`와 `뮤테이션(mutation)`을 나누는데 내부적으로 들어가면 사실상 이 둘은 별 차이가 없다.
**쿼리는 데이터를 읽는데(R)** 사용하고, **뮤테이션은 데이터를 변조(CUD)** 하는데 사용한다는 개념 적인 규약을 정해 놓은 것 뿐임. (_CRUD_)

## 오퍼레이션 네임과 변수

```graphQL
{
  human(id: "1000") {
    name
    height
  }
}

query HeroNameAndFriends($episode: Episode) {
  hero(episode: $episode) {
    name
    friends {
      name
    }
  }
}
```

하나 는 앞에 `query`가 붙어있고, 다른 하나는 처음 시작이 중괄호(‘{‘) 문자가 붙어있습니다. 이것을 이해하는데에는 `오퍼레이션 네임과 변수`의 쓰임새를 살펴보는 것이 도움이 된다.

일반적인 경우 클라이언트에서 `static`한 쿼리문을 작성하지는 않을 것입니다. 주로 정보를 불러올때 id 값이나, 다른 인자 값을 가지고 데이터를 불러 올 텐데,
gql에는 쿼리에 `변수`라는 개념이 있는데, 이 개념은 이러한 용처를 위해 존재 하는 것 이다.

gql을 구현한 클라이언트에서는 이 변수에 프로그래밍으로 값을 할당 할 수 있는 함수 인터페이스가 존재합니다. `react apollo client`의 경우에는 `variables` 라는 파라메터에 원하는 값을 넣어주면 된다.

```graphql
query getStudentInfomation($studentId: ID){
  personalInfo(studentId: $studentId) {
    name
    address1
    address2
    major
  }
  classInfo(year: 2018, studentId: $studentId) {
    classCode
    className
    teacher {
      name
      major
    }
    classRoom {
      id
      maintainer {
        name
      }
    }
  }
  SATInfo(schoolCode: 0412, studentId: $studentId) {
    totalScore
    dueDate
  }
}
```

_오퍼레이션 네임 쿼리는 매우 편리 합니다. 굳이 비유하자면 쿼리용 함수_

> 데이터베이스 에서의 프로시져(procedure) 개념과 유사하다고 생각하면 됩니다.

이 개념 덕분에 여러분은 REST API를 호출할때와 다르게, 한번의 인터넷 네트워크 왕복으로 원하는 모든 데이터를 가져 올 수 있다.
데이터베이스의 프로시져는 DBA 혹은 백앤드프로그래머가 작성하고 관리 하였지만,
**gql 오퍼레이션 네임 쿼리는 클라이언트 프로그래머가 작성하고 관리** 합니다.

gql이 제공하는 추가 기능 덕분에 백엔드 프로그래머와 프론트엔드 프로그래머의 협업 방식 에도 영향을 주는데
이전 협업 방식(REST API)에서는 프론트앤드 프로그래머는 백앤드 프로그래머가 작성하여 전달하는 API의 request / response의 형식에 의존하게 된다 하지만,
gql을 사용한 방식에 서는 이러한 의존도가 많이 사라진다. _다만 여전히 데이터 schema에 대한 협업 의존성은 존재._

## 스키마/타입(schema/type)

```graphql
type Character {
  name: String!
  appearsIn: [Episode!]!
}
```

- 오브젝트 타입 : Character
- 필드 : name, appearsIn
- 스칼라 타입 : String, ID, Int 등
- 느낌표(!) : 필수 값을 의미(non-nullable)
- 대괄호([, ]) : 배열을 의미(array)

## 리졸버(resolver)

gql 에서는 데이터를 가져오는 구체적인 과정을 직접 구현해야한다.
gql 쿼리문 파싱은 대부분의 gql 라이브러리에서 처리를 하지만, gql에서 데이터를 가져오는 구체적인 과정은 resolver(이하 리졸버)가 담당하고, 이를 직접 구현 해야 한다는 것이다.
리졸버를 직접 구현해야하는 부담은 있지만, 이를 통해서 데이터 source의 종류에 상관 없이 구현이 가능

> 데이터베이스 사용시, 데이터를 가져오기 위해서 sql을 작성 했습니다. 또한, 데이터베이스에는 데이터베이스 어플리케이션을 사용하여 데이터를 가져오는 구체적인 과정이 구현 되어 있습니다.

> 리졸버를 통해 데이터를 데이터베이스에서 가져 올 수 있고, 일반 파일에서 가져 올 수 있고, 심지어 http, SOAP와 같은 네트워크 프로토콜을 활용해서 원격 데이터를 가져올 수 있다. 덧붙이면, 이러한 특성을 이용하면 legacy 시스템을 gql 기반으로 바꾸는데 활용 할 수 있습니다.

gql 쿼리에서는 각각의 필드마다 함수가 하나씩 존재 한다고 생각.
이 함수는 다음 타입을 반환합니다. 이러한 각각의 함수를 resolver라고 합니다. 만약 필드가 스칼라 값인 경우에는 실행이 종료됩니다. 하지만 필드의 타입이 스칼라 타입이 아닌 우리가 정의한 타입이라면 해당 타입의 리졸버를 호출되게 됩니다.

### 예제

```graphQL
type Query {
  users: [User]
  user(id: ID): User
  limits: [Limit]
  limit(UserId: ID): Limit
  paymentsByUser(userId: ID): [Payment]
}

type User {
	id: ID!
	name: String!
	sex: SEX!
	birthDay: String!
	phoneNumber: String!
}

type Limit {
	id: ID!
	UserId: ID
	max: Int!
	amount: Int
	user: User      # User 1:1
}

type Payment {
	id: ID!
	limit: Limit!   # Limit 1:N
	user: User!     # User  1:N
	pg: PaymentGateway!
	productName: String!
	amount: Int!
	ref: String
	createdAt: String!
	updatedAt: String!
}
```

**연쇄적 리졸버 호출**
이점이 바로 gql이 Graph라는 단어를 쓴 이유가 아닐까?
연쇄 리졸버 호출은 여러모로 장점이 있다. 연쇄 리졸버 특성을 잘 활용하면 `DBMS`의 관계에 대한 쿼리를 매우 쉽고, 효율적으로 처리 할 수 있다.
예를들어 gql의 query에서 어떤 타입의 필드 중 하나가 해당 타입과 1:n의 관계를 맺고 있다고 가정해보자.

```graphQL
{
  paymentsByUser(userId: 10) {
    id
    amount
  }
}
```

```graphQL
{
  paymentsByUser(userId: 10) {
    id
    amount
    user {         # User
      name
      phoneNumber
    }
  }
}
```

위 두 쿼리는 동일한 쿼리명을 가지고 있지만, 호출 되는 리졸버 함수의 갯수는 아래가 더 많다.
각각의 리졸버 함수에는 내부적으로 데이터베이스 쿼리가 존재. 즉, 쿼리에 맞게 필요한 만큼만 최적화하여 호출 할 수 있다는 의미
내부적으로 로직 설계를 어떻게 하느냐에 따라서 달라 질 수 있겠지만, 이러한 재귀형의 리졸버 체인을 잘 활용 한다면, 효율적인 설계가 가능하다.

> 기존에 REST API 시대에는 정해진 쿼리는 무조건 전부 호출이 되었습니다.

#### 정리

- 스키마에는 쿼리를 정의해 두고 데이터 요구 사항에 대한 내용은 들어있지만 실제로 데이터를 가져오는 일은 스키마가 아닌 리졸버의 역할
- 리졸버는 특정 필드의 데이터를 반환하는 함수
- 스키마에 정의된 타입과 형태(shape)에 따라 데이터를 반환
- 비동기로 작성할 수 있으며 REST API, 데이터베이스, 혹은 기타 서비스의 데이터를 가져오거나 업데이트 작업을 할 수 있다.

## 인트로스펙션(introspection)

기존 서버-클라이언트 협업 방식에서는 연동규격서라고 하는 API 명세서를 주고 받는 절차가 반드시 필요 했습니다.
프로젝트 관리 측면에서 관리해야 할 대상의 증가는 작업의 복잡성 및 효율성 저해를 의미,
API 명세서는 때때로 관리가 제대로 되지 않아, 인터페이스 변경 사항을 제때 문서에 반영하지 못하기도 하고, 제 타이밍에 전달 못하곤 한다.

이러한 REST의 API 명세서 공유와 같은 문제를 해결하는 것이 gql의 인트로스펙션 기능
gql의 인트로스펙션은 서버 자체에서 현재 서버에 정의된 스키마의 실시간 정보를 공유를 할 수 있게 한다.
이 스키마 정보만 알고 있으면 클라이언트 사이드에서는 따로 연동규격서를 요청 할 필요가 없게 된다.

![아폴로 서버에서 제공하는 gql IDE](http://tech.kakao.com/files/graphql-apollo-ide.png)
_아폴로 서버에서 제공하는 gql IDE_

위의 화면을 참고하면, 프로그래머는 인트로스펙션을 활용하여, 직접 쿼리 및 뮤테이션, 필드 스키마를 확인 할 수 있습니다. 물론 보안상의 이슈로 상용환경에서는 이러한 스키마의 공개는 신중해야 합니다. 대부분의 라이브러리는 해당기능을 켜고 끄게 하는 옵션이 존재합니다.

---

[그래프큐엘 docs](https://graphql.org/learn/)
[GraphQL 이란?](https://tech.kakao.com/2019/08/01/graphql-basic/#graphql%EC%9D%84-%ED%99%9C%EC%9A%A9-%ED%95%A0-%EC%88%98-%EC%9E%88%EA%B2%8C-%EB%8F%84%EC%99%80%EC%A3%BC%EB%8A%94-%EB%8B%A4%EC%96%91%ED%95%9C-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%EB%93%A4)
[GraphQL & ApolloClient - 얄코](https://www.youtube.com/watch?v=9BIXcXHsj0A&t=1833s&ab_channel=%EC%96%84%ED%8C%8D%ED%95%9C%EC%BD%94%EB%94%A9%EC%82%AC%EC%A0%84)
