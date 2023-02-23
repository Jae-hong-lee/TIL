# GraphQL 이란?

![](https://upload.wikimedia.org/wikipedia/commons/thumb/1/17/GraphQL_Logo.svg/500px-GraphQL_Logo.svg.png)
**페이스북에서 만든 API 를 위한 쿼리 언어** 라고 할 수 있다.
그렇다면, 같은 쿼리 언어인 SQL (Structed Query Language) 비교해볼 수 있는데 둘은 목적에서부터 다르다.

`GraphQL` 은 **웹 클라이언트가 데이터를 서버로부터 효율적으로 가져오는 것**이 목적인 쿼리 언어이고,
`SQL` 은 **데이터베이스 시스템에 저장된 데이터를 효율적으로 가져오는 것**이 목적인 쿼리 언어라는 차이점이 있다.

`sql`의 문장(statement)은 주로 백앤드 시스템에서 작성하고 호출 하는 반면,
`gql`의 문장은 주로 클라이언트 시스템에서 작성하고 호출 한다.

## GraphQL의 구조

### SQL 예시

```sql
SELECT plot_id, species_id, sex, weight, ROUND(weight / 1000.0, 2) FROM surveys;
```

### GQL 예시

```gql
{
  hero {
    name
    friends {
      name
    }
  }
}
```

#### 쿼리/뮤테이션(query/mutation)

![GraphQL 쿼리문(좌측)과 응답 데이터 형식(우측)](http://tech.kakao.com/files/graphql-example.png)
_GraphQL 쿼리문(좌측)과 응답 데이터 형식(우측)_

쿼리와 뮤테이션 그리고 응답 내용의 구조는 상당히 직관적이고, 요청하는 쿼리문의 구조와 응답 내용의 구조는 거의 일치한다.

gql에서는 굳이 `쿼리(query)`와 `뮤테이션(mutation)`을 나누는데 내부적으로 들어가면 사실상 이 둘은 별 차이가 없어보인다.
**쿼리는 데이터를 읽는데(R)** 사용하고, **뮤테이션은 데이터를 변조(CUD)** 하는데 사용한다는 개념 적인 규약을 정해 놓은 것 뿐입니다. (_CRUD_)

## REST API 와 비교

REST API는 URL, METHOD등을 조합하기 때문에 다양한 Endpoint가 존재한다.
반면, gql은 단 하나의 Endpoint가 존재하는데, gql API에서는 불러오는 데이터의 종류를 **쿼리 조합을 통해서 결정**하게 된다.

> `쿼리 조합을 통해서 결정`이게 무슨 뜻이냐?
> REST API에서는 각 Endpoint마다 데이터베이스 SQL 쿼리가 달라지는 반면, gql API는 gql 스키마의 타입마다 데이터베이스 SQL 쿼리가 달라진다.

![HTTP와 gql의 기술 스택 비교](https://tech.kakao.com/files/graphql-stack.png)
HTTP와 gql의 기술 스택 비교

![REST API와 GraphQL API의 사용](http://tech.kakao.com/files/graphql-mobile-api.png)
REST API와 GraphQL API의 사용

위 그림처럼, gql API를 사용하면 여러번 네트워크 호출을 할 필요 없이, 한번의 네트워크 호출로 처리 할 수 있다.

---

#### 정리

그래프 큐엘은 페이스북에서 만든 새로운 쿼리 언어다.
데이터 주고 받기가 가능하며
그래프+쿼리 언어라고 보면된다.
즉, API에 접목 시킬 수 있는 쿼리 언어이다.

---

[그래프큐엘 docs](https://graphql.org/learn/)
[GraphQL이 무엇인가?](https://velopert.com/2318)
[그래프큐엘 개념](https://tech.kakao.com/2019/08/01/graphql-basic/)
