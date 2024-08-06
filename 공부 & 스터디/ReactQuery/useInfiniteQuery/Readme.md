# useInfinityQuery

`useInfinityQuery`는 React Query 라이브러리에서 제공하는 훅 중 하나이다.
무한 스크롤링과 같은 무한 데이터 페칭을 간편하게 구현할 수 있도록 도와주는데, 이를 사용하면 데이터를 페이지 단위로 불러와 무한히 스크롤할 수 있는 기능을 쉽게 구현할 수 있다.

`useInfinityQuery`는 `fetchNextPage`와 같은 메서드를 제공하여 다음 페이지의 데이터를 가져오는 것을 관리 할 수 있는데 예제를 통해 알아보자!

### Ex

1. API 요청 함수 정의

```js
import axios from "axios";

// API로부터 프로젝트 데이터를 받아오는 함수
const fetchProjects = async ({ pageParam = 1 }) => {
  // axios를 사용하여 API 요청을 보냄
  const response = await axios.get("/api/projects", {
    params: {
      page: pageParam, // pageParam을 통해 페이지 번호를 전달
    },
  });
  return response.data; // API로부터 받은 데이터를 반환
};
```

2. React 컴포넌트에 `useInfiniteQuery` 사용

```js
import React from "react";
import { useInfiniteQuery } from "react-query";
import { fetchProjects } from "./api"; // fetchProjects 함수를 정의한 파일 경로

const Projects = () => {
  // useInfiniteQuery 훅을 사용하여 프로젝트 데이터를 불러옴
  const {
    data, // 모든 페이지의 데이터를 포함한 객체
    error, // 에러가 발생했을 때의 에러 객체
    fetchNextPage, // 다음 페이지를 불러오는 함수
    hasNextPage, // 다음 페이지가 있는지 여부를 나타내는 불리언 값
    isFetching, // 데이터를 가져오는 중인지 여부를 나타내는 불리언 값
    isFetchingNextPage, // 다음 페이지를 가져오는 중인지 여부를 나타내는 불리언 값
    status, // 쿼리의 현재 상태 ('loading', 'error', 'success' 중 하나)
  } = useInfiniteQuery("projects", fetchProjects, {
    // 다음 페이지의 파라미터를 정의하는 함수
    getNextPageParam: (lastPage, pages) => {
      return lastPage.nextPage ?? false; // nextPage가 있으면 반환, 없으면 false 반환
    },
  });

  // 데이터 로딩 중일 때의 화면
  if (status === "loading") return <p>Loading...</p>;
  // 에러 발생 시의 화면
  if (status === "error") return <p>Error: {error.message}</p>;

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
      <div>
        {/* 'Load More' 버튼, 클릭 시 다음 페이지 데이터를 불러옴 */}
        <button
          onClick={() => fetchNextPage()}
          disabled={!hasNextPage || isFetchingNextPage} // 다음 페이지가 없거나, 다음 페이지를 가져오는 중이면 버튼 비활성화
        >
          {isFetchingNextPage
            ? "Loading more..." // 다음 페이지를 가져오는 중일 때의 버튼 텍스트
            : hasNextPage
            ? "Load More" // 다음 페이지가 있을 때의 버튼 텍스트
            : "No More Projects"}{" "}
          // 더 이상 가져올 페이지가 없을 때의 버튼 텍스트
        </button>
      </div>
      <div>{isFetching && !isFetchingNextPage ? "Fetching..." : null}</div> {/* 데이터를 가져오는 중일 때의 텍스트 */}
    </div>
  );
};

export default Projects;
```

`fetchProjects` 함수: API로부터 데이터를 받아오는 함수, pageParam을 사용하여 페이지 번호를 전달합니다.
`useInfiniteQuery` 훅: 이 훅을 사용하여 무한 스크롤링을 구현한다. getNextPageParam 옵션을 통해 다음 페이지의 파라미터를 정의함.
**컴포넌트 렌더링**: data.pages를 통해 각 페이지의 데이터를 렌더링한다. fetchNextPage를 호출하여 다음 페이지의 데이터를 가져온다.
`queryKey`: 쿼리를 식별하는 키, 이 예제에서는 'projects'로 설정되어있음
`queryFn`: 데이터를 가져오는 함수
`getNextPageParam`: 다음 페이지의 파라미터를 정의하는 함수, lastPage와 pages 인자를 사용하여 다음 페이지를 결정

위 코드를 통해 `useInfiniteQuery`를 사용하여 데이터를 페이지 단위로 불러오고, 버튼 클릭 시 다음 페이지의 데이터를 불러오는 무한 스크롤링을 구현할 수 있다.

> 추가적으로 스크롤 이벤트를 감지하여 자동으로 다음페이지를 불러오는 로직을 구현한다면 더욱 사용자 친화적인 무한 스크롤 기능을 완성할 수 있을 것!
