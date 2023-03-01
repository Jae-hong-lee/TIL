# Apollo-Client

Apollo Client는 GrpahQL API를 호출하기 위해 사용되는 라이브러리.

## GraphQL을 활용 할 수 있게 도와주는 다양한 라이브러리들

gql 자체는 쿼리 언어입니다. 이것만으로는 할 수 있는 것이 없다.
gql을 실제 구체적으로 활용 할 수 있도록 도와주는 라이브러리들이 몇가지 존재 한다.
gql 자체는 개발 언어와 사용 네트워크에 완전히 독립적임. 이를 어떻게 활용 할지는... (당신에게)

대표적인 gql 라이브러리 셋에 대한 링크는 2개가 있다.

- 릴레이(Relay)
- 아폴로(Apollo GraphQL)

오늘은 사용해봤던 아폴로에 대해서 이야기해 보려 한다.

## 근데 왜 Apollo Client 일까?

많은 라이브러리 중에서 `Apollo Client`를 사용하는 이유는 캐싱이 잘 되어있고, 데이터를 선언적으로 접근하기 때문에 적은 코드로 생산성 높은 개발을 할 수 있다는 장점을 가지고 있기 때문이다.
`Apollo Client`에서 캐싱은 정규화가 되어 있어서 여러 컴포넌트에서 데이터의 일관성을 유지 시켜줌
`Apollo Client`의 주요 특징 중 하나는 로컬 인메모리 정규화된 캐시를 사용한다는 것!!

```javascript
import { ApolloClient, InMemoryCache } from "@apollo/client";

const client = new ApolloClient({
  cache: new InMemoryCache(),
});
```

캐시를 Apollo Client에 보내면, Apollo Client가 쿼리 응답을 받을 때 마다, 자체 캐시 안에서 분리된 별개의 엔트리에 자동적으로 데이터를 저장한다.
처음으로 쿼리할 때 데이터를 가져오는 흐름은 다음과 같다.

![처음쿼리 흐름도](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FkY2eq%2FbtrENEQZypk%2Fy23KyUZiP1CkKqiTuUniv0%2Fimg.png)
_Apollo Client 첫 쿼리 흐름도_

**같은 쿼리를 두 번째 이후부터 부를 때는 흐름이 달라진다!!!**

![두번째 쿼리 흐름도](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fxl9Oe%2FbtrENuufiJs%2Ff6vASKP8oObkcPcFituKm0%2Fimg.png)
_Apollo Client 두번째 쿼리 흐름도_

`Apollo Client` _캐시는_ **아주 변경이 가능한 구조여서 각각의 타입과 필드를 커스터마이즈 할 수 있다.**
또한 캐시를 아직 GQL 서버로부터 패치되지 않은 로컬 데이터를 저장하고 **상호작용 하는데 사용할 수도 있다.**

## 데이터의 저장과 정규화

Apollo Client의 InMemoryCache는 데이터를 flat lookup table 형태로 저장
따라서 객체 안에서 각각을 서로서로 참조할 수 있다. 이 객체는 GQL 쿼리에서 리턴받는 객체와 일치한가. 여러 쿼리를 하더라도 하나의 객체로 묶여져서 캐싱된다.
캐시는 플랫하지만, GQL 쿼리로 응답받은 객체는 때때로 플랫하지 않다. 사실, 깊게 중첩이 될 수 있는 구조

```JSON
{
  "data": {
    "person": {
      "__typename": "Person",
      "id": "cGVvcGxlOjE=",
      "name": "Luke Skywalker",
      "homeworld": {
        "__typename": "Planet",
        "id": "cGxhbmV0czox",
        "name": "Tatooine"
      }
    }
  }
}
```

이 응답에는 Person 객체가 포함되어 있고, 차례대로 Person 객체의 homeworld 필드에서 Planet 객체를 포함한다.
따라서 이렇게 중첩된 데이터를 평평한 데이터로 바꿔서 캐시에 저장을 해 주어야 하는데, 이 과정에서 사용하는 방법이 정규화(normalization)다.

> Apollo Client 캐시가 쿼리 데이터를 받으면 다음과 같은 과정들을 수행한다
>
> - 객체를 확인한다. (Identify objects)
> - 캐시 ID를 만든다. (Generate cache IDs)
> - 객체의 필드를 레퍼런스로 대체한다. (Replace object field with references)
> - 정규화된 객체를 저장한다.

<!-- **React 앱에서 Apollo Client를 사용하여 GraphQL API를 호출하는 방법에 대해서도 알아보자.**
사실, GraphQL API를 호출할 때, 반드시 Apollo Client와 같은 전용 클라이언트가 필요한 것은 아니다.
그리고, React 없이 순수하게 Apollo Client만을 사용하는 방법 또한 있다. -->

---

[Apollo Client 사용법](https://www.daleseo.com/graphql-react-apollo-client/)
[Apollo Client 캐싱](https://devowen.com/458)
