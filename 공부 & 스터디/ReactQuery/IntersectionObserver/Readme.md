# IntersectionObserver

> useInfinityQuery 글 중... : 추가적으로 스크롤 이벤트를 감지하여 자동으로 다음페이지를 불러오는 로직을 구현한다면 더욱 사용자 친화적인 무한 스크롤 기능을 완성할 수 있을 것!

스크롤 이벤트를 감지하여 자동으로 다음 페이지를 불로오는 로직을 구현하는 방법은 여러 가지가 있지만, 가장 일반적인 방법 중 하나인 `useInfinityQuery` API 에 대해서 알아보자.

`useInfinityQuery`는 특정 요소가 뷰포트에 들어오거나 나갈 때 콜백을 실행하도록 설정할 수 있다.
이를 통해 사용자가 페이지 하단에 도달했을 때 다음 페이지의 데이터를 불러오게 구현할 수 있다.

### 예제

1. API 요청 함수 정의

```js
import axios from "axios";

// API로부터 프로젝트 데이터를 받아오는 함수
const fetchProjects = async ({ pageParam = 1 }) => {
  const response = await axios.get("/api/projects", {
    params: {
      page: pageParam, // pageParam을 통해 페이지 번호를 전달
    },
  });
  return response.data; // API로부터 받은 데이터를 반환
};
```

2. React 컴포넌트에 useInfiniteQuery와 IntersectionObserver 사용

```js
import React, { useRef, useEffect, useCallback } from "react";
import { useInfiniteQuery } from "react-query";
import { fetchProjects } from "./api"; // fetchProjects 함수를 정의한 파일 경로

const Projects = () => {
  // useInfiniteQuery 훅을 사용하여 프로젝트 데이터를 불러옴
  const {
    data,
    error,
    fetchNextPage,
    hasNextPage,
    isFetching,
    isFetchingNextPage,
    status,
  } = useInfiniteQuery("projects", fetchProjects, {
    // 다음 페이지의 파라미터를 정의하는 함수
    getNextPageParam: (lastPage, pages) => {
      return lastPage.nextPage ?? false; // nextPage가 있으면 반환, 없으면 false 반환
    },
  });

  // 감지할 요소에 대한 ref
  const loadMoreRef = useRef();

  // IntersectionObserver 콜백
  const observer = useRef();

  // 마지막 요소의 ref를 업데이트하고 IntersectionObserver를 설정하는 함수
  const lastElementRef = useCallback(
    (node) => {
      if (isFetchingNextPage) return; // 다음 페이지를 가져오는 중이면 설정하지 않음
      if (observer.current) observer.current.disconnect(); // 기존의 observer를 해제
      observer.current = new IntersectionObserver((entries) => {
        if (entries[0].isIntersecting && hasNextPage) {
          fetchNextPage(); // 요소가 뷰포트에 들어오면 다음 페이지 데이터를 가져옴
        }
      });
      if (node) observer.current.observe(node); // 요소를 관찰
    },
    [isFetchingNextPage, fetchNextPage, hasNextPage]
  );

  if (status === "loading") return <p>Loading...</p>; // 데이터 로딩 중일 때의 화면
  if (status === "error") return <p>Error: {error.message}</p>; // 에러 발생 시의 화면

  return (
    <div>
      {/* 각 페이지의 데이터를 렌더링 */}
      {data.pages.map((page, index) => (
        <React.Fragment key={index}>
          {/* 각 프로젝트를 렌더링 */}
          {page.items.map((project) => (
            <div key={project.id}>{project.name}</div>
          ))}
        </React.Fragment>
      ))}
      <div ref={lastElementRef}>
        {" "}
        {/* 마지막 요소에 ref를 설정 */}
        {isFetchingNextPage ? "Loading more..." : "Load More"}{" "}
        {/* 다음 페이지를 가져오는 중일 때의 텍스트 */}
      </div>
      <div>{isFetching && !isFetchingNextPage ? "Fetching..." : null}</div> {/* 데이터를 가져오는 중일 때의 텍스트 */}
    </div>
  );
};

export default Projects;
```

### 간단한 동작설명

1. **`loadMoreRef`**: 스크롤 이벤트를 감지할 요소에 대한 레퍼런스, 페이지의 마지막 요소로 설정

2. **`IntersectionObserver` 콜백 설정**:

   - `observer`는 `IntersectionObserver` 인스턴스를 담기 위한 `useRef`.
   - `lastElementRef`는 `useCallback`을 사용하여, 스크롤 감지 요소에 대한 레퍼런스를 업데이트
   - `isFetchingNextPage`가 `true`일 때는 `IntersectionObserver`를 설정하지 않는다.
   - `observer.current`를 초기화하고, 만약 이전에 설정된 `observer`가 있다면 `disconnect` 한다
     > 연결을 끊는다.
   - 새로운 `IntersectionObserver`를 생성하여 콜백을 설정, 콜백에서는 요소가 뷰포트에 들어왔는지 (`isIntersecting`) 확인하고, `hasNextPage`가 `true`일 때 `fetchNextPage`를 호출.
   - `node`가 존재하면 `observer.current`가 해당 노드를 관찰

3. **`return` 부분**:
   - 데이터 페이지를 렌더링
   - 마지막 요소로 `ref={lastElementRef}`를 설정하여 `IntersectionObserver`가 감지
   - `useInfinityQuery에서` 만들었던 버튼을 클릭하는 대신, 자동으로 다음 페이지를 불러오게 한다.

이 코드를 통해 사용자가 페이지 하단으로 스크롤할 때 자동으로 다음 페이지의 데이터를 불러오는 무한 스크롤 기능을 구현할 수 있습니다.

##### IntersectionObserver 역할과 동작

1. IntersectionObserver 생성

```js
observer.current = new IntersectionObserver((entries) => {
  if (entries[0].isIntersecting && hasNextPage) {
    fetchNextPage(); // 요소가 뷰포트에 들어오면 다음 페이지 데이터를 가져옴
  }
});
```

- 지정된 콜백 함수를 사용하여 특정 요소가 뷰포트와 교차하는지 감지
- `entries[0].isIntersecting`은 요소가 뷰포트와 교차하고 있는지를 나타내는 Boolean값
- `hasNextPage`는 추가 페이지가 있는지를 나타내고, 조건이 충족되면 `fetchNextPage`호출

2. 요소 관찰

```js
if (node) observer.current.observe(node); // 요소를 관찰
```

- `node`존재할 경우, 즉 마지막 요소가 렌더링된 경우 해당 요소를 관찰
- 관찰할 요소(`node`)가 뷰포트에 들어오면 `IntersectionObserver` 의 콜백 함수가 실행.

3. 기존 관찰 중지

```js
if (observer.current) observer.current.disconnect(); // 기존의 observer를 해제
```

새로운 요소를 관찰하기 전에 기존의 관찰을 중지
이는 중복 관찰을 방지하고, 성능을 최적화하기 위함
