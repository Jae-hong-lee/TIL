# React-Query

![React-Query](https://github.com/Jae-hong-lee/TIL/assets/72030487/a2b419a0-e7a4-4c0a-950b-619e558cf8af)

React-Query 는 데이터 Fetching, Caching, 동기화, 서버 데이터 업데이트 등을 쉽게 만들어 주는 라이브러리이다.
React에서 상태를 관리하기 위해 Redux, Recoil 등 여러 라이브러리가 존재하는데 React-Query는 간단하게 이야기해서 React 환경에서의 비동기 Query 과정을 도와주는 라이브러리인 것이다.

> 「if(kakao)2021 - 카카오페이 프론트엔드 개발자들이 React Query를 선택한 이유」
>
> 1.  React Query는 React Application에서 서버 상태를 불러오고, 캐싱하며, 지속적으로 동기화하고 업데이트하는 작업을 도와주는 라이브러리입니다.
> 2.  복잡하고 장황한 코드가 필요한 다른 데이터 불러오기 방식과 달리 React Component 내부에서 간단하고 직관적으로 API를 사용할 수 있습니다.
> 3.  더 나아가 React Query에서 제공하는 캐싱, Window Focus Refetching 등 다양한 기능을 활용하여 API 요청과 관련된 번잡한 작업 없이 “핵심 로직”에 집중할 수 있습니다.

---

### 캐싱(Caching)

캐싱이란 특정 데이터의 복사본을 가지고 동일한 데이터의 재접근 속도를 높이는 것을 말한다.
React-Query는 캐싱을 통해 동일한 데이터에 대한 반복적인 **비동기 데이터 호출**을 방지하고,
불필요한 API 콜을 줄여 서버에 대한 **부하를 줄이는** 결과를 가져오는 것.

#### 데이터갱신

만일 서버 데이터를 불러와 데이터를 캐싱하고, 실제 서버 데이터를 확인했을 때 서버 상에서 데이터 상태가 변경되었다면, 사용자는 실제 데이터가 아닌 변경 전 캐싱되어 있는 데이터를 볼 수 밖에 없다.
이는 사용자에게 잘못된 정보를 보여주게 되고 에러를 만들게 된다.

그렇다면 좋은 캐싱 기능을 제공하는 것은 결국 필요한 상황에 적절하게 데이터를 갱신해줘야하는 것이다.
크게 보면 3가지 상황으로 나눌 수 있는데 ~~3가지 상황에 맞추어~~ React-Query는 기본적인 옵션들을 제공한다.

1. 현재화면을 보고 있을때
2. 페이지 전환
3. 페이지 전환 없이 이벤트가 발생해 데이터 요청을 할 때

```js
refetchOnWindowFocus, //default: true
refetchOnMount, //default: true
refetchOnReconnect, //default: true
staleTime, //default: 0
cacheTime, //default: 5분 (60 * 5 * 1000)
```

옵션들을 통해 우리가 React-Query가 어떤 시점에 데이터를 Refetching 하는지 알 수 있다.

1. 브라우저에 포커스가 들어온 경우(refetchOnWindowFocus)
2. 새로운 컴포넌트 마운트가 발생한 경우(refetchOnMount)
3. 네트워크 재연결이 발생한 경우(refetchOnReconnect)

#### staleTime? cacheTime ?

> React-Query에서는 최신의 데이터를 fresh한 데이터, 기존의 데이터를 stale한 데이터

staleTime은 최신의 데이터가 기존 데이터로 변경되는 데 걸리는 시간
fresh 상태일 때는 Refetch 트리거(위 3가지 경우)가 발생해도 Refresh가 일어나지 않는다.
기본값이 0 이므로 따로 설정해주지 않으면 Refetch 트리거가 발생했을 때 무조건 Refetch가 발생하게 됨

CacheTime은 데이터가 inactive(활동하지 않는)한 상태일 때 캐싱된 상태로 남아있는 시간
특정 컴포넌트가 **언마운트(페이지 전환 등으로 화면에서 사라질 때)** 되면 inactive 상태로 바뀌고, 이때 데이터는 CacheTime 만큼 유지가 되게 된다.
cacheTime 이후 데이터는 [가비지 콜렉터](https://blog.metafor.kr/163)로 수집되어 메모리에서 해제된다.
만일 cacheTime이 지나지 않았는데 해당 데이터를 사용하는 컴포넌트가 다시 **마운트** 되면, 새로운 데이터를 fetch 해오는 동안 캐싱된 데이터를 보여준다.
즉, 캐싱된 데이터를 계속 보여주는게 아니라 fetch 하는 동안 보여주게 되는 것.

### Client 데이터, Server 데이터

프로젝트 규모가 커지고 관리해야할 데이터가 넘치면 Client에서 관리하는 데이터와 Server 데이터를 분리해야 할 필요성을 느끼게 된다.

> 간단하게 생각해서 비동기 API 호출을 통해 불러오는 데이터들을 Server 데이터라고 할 수 있다.
> 실제 Client 데이터의 경우 전역 상태 관리 라이브러리(Recoil, Redux..)들을 통해 잘 관리되어 오고 있다.
> 문제는 이러한 라이브러리들이 Server 데이터까지도 관리해야하는 상황이 발생하게 되는 것.

위의 상태 관리 라이브러리에도 비동기 함수를 처리하는 로직이 존재, 서드 파티 라이브러리를 지원하는 것도 많다. 그러나, 이들이 Client 데이터와 Server 데이터를 완벽히 분리하여 관리에 용이하도록 충분한 기능을 지원한다고 보기에는 어렵다.
라이브러리들은 Client 데이터를 관리하는데 로직이 집중되어 있고, Server데이터까지 효율적으로 관리하는 데에는 한계점이 있는 것.

React-Query는 이러한 문제에 대한 해결책 또한 제시해준다.

```js
// v4의 문법 기반
const { data, isLoading } = useQueries({
	['unique-key'],
	() => {
		return api({
			url: URL,
			method: 'GET',
		});
	},
	{
		onSuccess: (data) => {
			// data로 이것저것 하는 로직
		}
	},
	{
		onError: (error) => {
			// error로 이것저것 하는 로직
		}
	}
})
```

위 코드에서는 컴포넌트 내부에서 위와 같은 로직을 통해 Server 데이터를 가져오고 있는데,
`onSuccess`와 `onError` 함수를 통해 fetch 요청이 성공했는지에 대한 분기를 아주 간단하게 구현할 수 있다.
이는 Server 데이터를 불러오는 과정에서 구현해야할 추가적인 설정들을 진행할 필요성을 못느끼게 해준다.

즉, Client 데이터는 `상태 관리 라이브러리`가 관리하고, Server 데이터는 `React-Query`가 관리하는 구조 이를 통해 우리는 Client 데이터와 Server 데이터를 온전하게 분리할 수 있다.

> React-Query가 가져온 Server데이터를 상태 관리 라이브러리를 통해 전역 상태로 가져올 수도 있다.
> 그러나 refetch가 여러 번 일어나는 상황에 매번 Server데이터를 전역 상태로 가져오는 것이 좋은지는 생각을 한번 해보자.

### 데이터를 다루는 React-Query

React-Query를 사용하다보면 에러를 겪거나 프론트엔드 라이브러리 코드를 조금보게 되면, React-Query 또한 React의 (ContextAPI)[https://velog.io/@dlwoxhd/context-api-redux-mobx-swr]를 기반으로 동작한다.

전체 Scope가 되며 하위 컴포넌트들의 server data를 관리하는 QueryClient가 존재하는데, 해당 QueryClient는 우리가 Query를 사용할 때 명시하는 Key를 기반으로 데이터를 저장하게 된다.
즉, QueryClient 는 단순히 서버에서 불러온 데이터를 저장하는 곳이 아니라, 데이터를 저장하는 용도로 사용되는 Context Store 와 동일한 역할을 한다는 것.
그 용도가 서버 데이터 관리에 초점을 두는 것일 뿐, 우리는 필요에 따라 QueryClient 또한 단순 데이터 저장소처럼 간주하고 자유자재로 활용할 수 있어야함.

### 대표적인 기능

React-Query에서 data fetching을 위해 제공하는 대표적인 기능들을 살펴보자.
기본적으로 `GET` 에는 **useQuery** 가,
`PUT`, `UPDATE`, `DELETE` 에는 **useMutation** 이 사용된다.

#### useQuery

- 첫 번째 파라미터로 unique key를 포함한 배열이 들어간다. 이후 동일한 쿼리를 불러올 때 유용하게 사용된다.
- 첫 번째 파라미터에 들어가는 배열의 첫 요소는 unique key로 사용되고, 두 번째 요소부터는 query 함수 내부의 파라미터로 값들이 전달된다.
- 두 번째 파라미터로 실제 호출하고자 하는 비동기 함수가 들어간다. 이때 함수는 Promise를 반환하는 형태여야 한다.
- 최종 반환 값은 API의 성공, 실패 여부, 반환값을 포함한 객체이다.

```js
import {
  QueryClient,
  QueryClientProvider,
  useQuery,
} from "@tanstack/react-query";

const queryClient = new QueryClient();

export default function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Example />
    </QueryClientProvider>
  );
}

function Example() {
  // useQuery
  const { isPending, error, data } = useQuery({
    queryKey: ["repoData"],
    queryFn: () =>
      // url
      fetch("https://api.github.com/repos/tannerlinsley/react-query").then(
        (res) => res.json()
      ),
  });

  // isPending
  if (isPending) return "Loading...";
  // Error
  if (error) return "An error has occurred: " + error.message;

  return (
    <div>
      <h1>{data.name}</h1>
      <p>{data.description}</p>
      <strong>👀 {data.subscribers_count}</strong>{" "}
      <strong>✨ {data.stargazers_count}</strong>{" "}
      <strong>🍴 {data.forks_count}</strong>
    </div>
  );
}
```

useQuery 함수가 반환하는 객체를 보면 `isPending` 을 통해 로딩 여부를, `error` 를 통해 에러 발생 여부를, data를 통해 성공 시 데이터를 반환할 수 있다.
`isPending` 과 `error` 를 이용하여 각 상황 별 분기를 쉽게 진행할 수 있다.

#### useMutation

`PUT`, `UPDATE`, `DELETE` 와 같이 값을 변경할 때 사용하는 API다. 반환값은 useQuery와 동일하다.

```js
function App() {
  const mutation = useMutation({
    mutationFn: (newTodo) => {
      // post
      return axios.post("/todos", newTodo);
    },
  });

  return (
    <div>
      {mutation.isLoading ? (
        "Adding todo..."
      ) : (
        <>
          {mutation.isError ? (
            <div>An error occurred: {mutation.error.message}</div>
          ) : null}

          {mutation.isSuccess ? <div>Todo added!</div> : null}

          <button
            onClick={() => {
              mutation.mutate({ id: new Date(), title: "Do Laundry" });
            }}
          >
            Create Todo
          </button>
        </>
      )}
    </div>
  );
}
```

위의 코드에서 볼 수 있듯이 반환값은 `useQuery` 와 동일하지만, 처음 사용 시에 `post` 비동기 함수를 넣어주었다.
이때 `useMutation` 의 첫 번째 파라미터에 비동기 함수가 들어가고, 두 번째 인자로 상황 별 분기 설정이 들어간다는 점이 차이이다.

실제 사용 시에는 `mutation.mutate` 메서드를 사용하고, 첫 번째 인자로 API 호출 시에 전달해주어야하는 데이터를 넣어주면 된다.

---

[카카오페이 프론트엔드 개발자들이 React Query를 선택한 이유](https://tech.kakaopay.com/post/react-query-1/)
[React-Query 총 정리](https://velog.io/@94applekoo/React-Query-%EC%B4%9D-%EC%A0%95%EB%A6%AC#1-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0)
[React-Query 개념잡기](https://velog.io/@kandy1002/React-Query-%ED%91%B9-%EC%B0%8D%EC%96%B4%EB%A8%B9%EA%B8%B0#reference)
